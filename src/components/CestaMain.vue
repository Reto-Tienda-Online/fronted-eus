<script setup>
import { useStore } from "vuex";
import { useRouter } from "vue-router";
import { onMounted, ref, reactive, watch } from "vue";
import axios from "axios";

const router = useRouter();
const store = useStore();
const juegosEnCarrito = ref([]);
const precioTotal = ref(0);

const seeGameDetails = (juego) => {
  console.log(juego);
  store.commit("setJuegoDetalle", juego);
  router.push("/juegoDetalle");
};
const getImageURL = (id) => {
  return `/imgs/juegos/${id}/2.webp`;
};

const getPrecioTotal = () => {
  precioTotal.value = 0;
  for (const item of juegosEnCarrito.value) {
    const juego = item["producto"];

    if (item.cantidad) {
      precioTotal.value += parseFloat(juego.precio_unitario) * item.cantidad;
    } else {
      precioTotal.value += parseFloat(juego.precio_unitario);
    }
  }
};

const payForAll = () => {
  router.push("/pago");
};

const deleteCartItem = (idItem) => {
  //DELETE FROM DATABASE
  const config = {
    method: "delete",
    maxBodyLength: Infinity,
    url: `https://api.donostipub.eus/carrocompra/${idItem}`,
    headers: {},
  };
  axios
    .request(config)
    .then((response) => {
      console.log(JSON.stringify(response.data));
    })
    .catch((error) => {
      console.log(error);
    });

  //DELETE FROM STORE
  store.commit("deleteItem", idItem);

  //DELETE FROM LOCAL STORAGE
  localStorage.removeItem("shopcart");

  //ACTUALIZAMOS EL STATE PARA QUE SE VISUALICEN LOS CAMBIOS EN LA PAGINA
  juegosEnCarrito.value = juegosEnCarrito.value.filter((item) => {
    return item.id !== idItem;
  });
};

const updateCantidad = (juego) => {
  const id_user = JSON.parse(localStorage.getItem("usuario")).id;

  let data = JSON.stringify({
    id_usuario: id_user,
    id_producto: juego["producto"].id,
    pagado: false,
    cantidad: juego.cantidad,
  });
  let config = {
    method: "put",
    maxBodyLength: Infinity,
    url: `https://api.donostipub.eus/carrocompra/${juego.id}`,
    headers: {
      accept: "application/json",
      "Content-Type": "application/json",
    },
    data: data,
  };
  axios
    .request(config)
    .then((response) => {
      console.log(JSON.stringify(response.data));
    })
    .catch((error) => {
      console.log(error);
    });
};

onMounted(() => {
  const idUser = JSON.parse(localStorage.getItem("usuario")).id;
  const path = `https://api.donostipub.eus/carrocompra?id_usuario=${idUser}`;
  axios
    .get(path)
    .then((response) => {
      juegosEnCarrito.value = response.data;
    })
    .catch((error) => {
      console.error(error);
    });

  // console.log(juegosEnCarrito.value);

  getPrecioTotal();
});

watch(
  juegosEnCarrito,
  () => {
    getPrecioTotal();
  },
  {
    deep: true,
  }
);
</script>

<template>
  <main class="flex flex-col justify-center md:flex-row w-auto mt-10">
    <!--COMPRAS-->
    <div v-if="juegosEnCarrito.length > 0" class="w-auto flex flex-col gap-5">
      <div
        v-for="(juego, index) in juegosEnCarrito"
        :key="juego.id"
        class="flex text-white w-auto mx-4"
      >
        <!--Contenido del juego a comprar a repetir segun la cantidad de juegos en el carrito-->
        <div
          class="flex flex-col md:flex-row justify-around bg-[#1f1f1f] w-full px-5 py-5 rounded-xl"
        >
          <div class="cover" @click="seeGameDetails(juego['producto'])">
            <picture
              ><img
                :alt="juego['producto'].producto"
                :src="getImageURL(juego['producto'].id)"
                loading="lazy"
                class="rounded-xl w-45 my-5 md:my-2 md:max-w-96 hover:cursor-pointer"
            /></picture>
          </div>
          <div class="w-full mx-2 flex flex-row md:flex-col text-md">
            <div class="name flex flex-col w-full">
              <!--Nombre del Juego-->
              <span title="New Cycle - Europe" class="font-bold text-lg">{{
                juego["producto"].producto
              }}</span>
              <div class="text-sm text-gray-300">
                {{ juego["producto"].plataforma.plataforma }}
              </div>
              <!--Delete icon-->
              <div
                class="text-sm py-6 text-gray-300 flex flex-row hover:underline cursor-pointer"
                @click="deleteCartItem(juego.id)"
              >
                <div><font-awesome-icon icon="trash" /></div>
                <div class="moveToWishlist ml-2">Erosketako orga ezabatu</div>
              </div>
            </div>
            <!--Precio Unitario y Cantidad-->
            <div class="text-md md:text-base">
              <div class="price text-sm md:text-2xl">
                {{ juego["producto"].precio_unitario }}€
              </div>
              <label
                class="text-base md:text-xl font-semibold md:font-bold my-3"
              >
                Kantitatea:
                <select
                  class="w-full mt-5 bg-gray-50 py-1 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                  data-max="10"
                  v-model="juego.cantidad"
                  @change="updateCantidad(juego)"
                >
                  <option value="0" disabled="disabled">0</option>
                  <option value="1">1</option>
                  <option value="2">2</option>
                  <option value="3">3</option>
                  <option value="4">4</option>
                  <option value="5">5</option>
                  <option value="6">6</option>
                  <option value="7">7</option>
                  <option value="8">8</option>
                  <option value="9">9</option>
                  <option value="10">10</option>
                </select>
              </label>
            </div>
          </div>
          <!---->
        </div>
      </div>
    </div>
    <div v-else>
      <h1 class="text-xl text-white text-center">
        Erosketako orga hutsik dago
      </h1>
      <picture>
        <img src="/imgs/empty_shopcart.png" class="max-w-96" />
      </picture>
      <h1 class="text-xl text-white text-center">
        Ez galdu denbora, erosi orain
      </h1>
    </div>
    <!--RESUMEN DE COMPRAS-->
    <div v-if="juegosEnCarrito.length > 0" class="mt-5 md:mt-0 flex flex-col">
      <div class="text-white md:w-full mx-4">
        <div
          class="flex flex-col justify-center w-full bg-[#1f1f1f] px-5 py-5 rounded-xl"
        >
          <div class="flex justify-between">
            <span>Prezioa</span> <span>{{ precioTotal.toFixed(2) }}€</span>
          </div>
          <div class="flex justify-between font-bold text-2xl my-3">
            <span>Subtotala</span> <span>{{ precioTotal.toFixed(2) }}€</span>
          </div>
          <button
            @click="payForAll"
            class="bg-resaltar flex justify-center rounded-lg px-2 py-4 my-5 font-bold"
          >
            Ordainketa egin
          </button>
          <div class="flex flex-row items-center justify-center">
            <span class="h-[0.5px] w-full bg-gray-300"></span>
            <span class="flex justify-center my-3 text-gray-300">o</span>
            <span class="h-[0.5px] w-full bg-gray-300"></span>
          </div>

          <a href="/" class="flex justify-center text-md text-gray-300">
            Ordainketarekin jarraitu
          </a>
        </div>
      </div>
    </div>
  </main>
</template>
