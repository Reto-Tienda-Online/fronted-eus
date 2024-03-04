<script setup>
import { ref, reactive, computed, onMounted } from "vue";
import Navbar from "./Navbar.vue";
import Comentario from "./Comentario.vue";
import axios from "axios";
import { useStore } from "vuex";
import { useRouter } from "vue-router";

const router = useRouter();
const store = useStore();
const juegoPasado = ref({});
const listaCompra = ref([]);
const comment = reactive({
  resena: "",
  id_usuario: null,
  contenido: "",
  id_juego: null,
  valoracion: 5,
});

const listaComentarios = ref([]);
const productsPerPage = ref(3);
const init = ref(0);
const end = ref(3);
const currentPage = ref(1);
const totalPages = ref(0);

const splitDescripcion = ref("");
const showAllDesc = ref(false);
const sendComment = () => {
  const id_user = JSON.parse(localStorage.getItem("usuario"))?.id;
  if (id_user !== undefined) {
    comment.id_usuario = id_user;
  } else {
    router.push("/login");
  }

  comment.id_juego = juegoPasado.value.id;
  let data = JSON.stringify(comment);
  let config = {
    method: "post",
    maxBodyLength: Infinity,
    url: "http://85.50.79.98:8080/resenas/",
    headers: {
      "Content-Type": "application/json",
    },
    data: data,
  };
  axios
    .request(config)
    .then((response) => {
      console.log(JSON.stringify(response.data));
      comment.resena = "";
      comment.contenido = "";
      alert("Comentario enviado con éxito");
    })
    .catch((error) => {
      console.log(error);
    });
  // console.log(comment.value)
  // console.log(juego)
};

//ARREGLAR METODO
const getImgURL = (id) => {
  const imgUrl = `/imgs/juegos/${id}/2.webp`;
  // console.log(imgUrl);
  return imgUrl;
};

const getLogoURL = (nombreLogo) => {
  return `/imgs/logos/${nombreLogo}.png`;
};

const showMore = computed(() => {
  splitDescripcion.value = juegoPasado.value.descripcion;

  if (splitDescripcion.value) {
    if (splitDescripcion.value.length >= 200) {
      splitDescripcion.value = splitDescripcion.value.slice(0, 200);
      splitDescripcion.value = splitDescripcion.value.slice(
        0,
        splitDescripcion.value.lastIndexOf(" ")
      );
      return true;
    } else {
      return false;
    }
  }
});

const showMoreText = () => {
  showAllDesc.value = !showAllDesc.value;
};

const sendCartData = (data) => {
  const id_user = JSON.parse(localStorage.getItem("usuario")).id;

  // console.log(id_user)
  const juegoAñadir = JSON.stringify({
    id_usuario: id_user,
    id_producto: data.id,
  });

  // console.log(juegoAñadir)
  const config = {
    method: "post",
    maxBodyLength: Infinity,
    url: "http://85.50.79.98:8080/carrocompra",
    headers: {
      "Content-Type": "application/json",
    },
    data: juegoAñadir,
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

const addItemToCart = (item) => {
  //PARA ISSAM
  if (!isInShopcart(item)) {
    //Verificamos si el usuario esta logueado
    const isLoggedIn = JSON.parse(localStorage.getItem("isLoggedIn"));

    if (isLoggedIn) {
      try {
        //COMPARTO ENTRE COMPONENTES
        store.commit("setShopItems", item);

        //AÑADO AL LOCAL STORAGE PARA QUE NO SE PIERDA LA SESION Y NO TENGA QUE CARGARLO DESDE LA BD
        localStorage.setItem("shopcart", JSON.stringify(store.state.shopCart));

        //HAGO UN POST A LA API
        sendCartData(item);
        // console.log(store.state.shopCart)

        alert(`El juego ${item.producto} se ha añadido al carrito`);

        router.push("/cesta");
      } catch (error) {
        console.error(error);
      }
    } else {
      alert("Saioa hasi behar dezu");
      router.push("/login");
    }
  } else {
    alert("Ya está en el carrito");
  }
};

const isInShopcart = (juego) => {
  //BD
  let isInShop = false;
  if (listaCompra.value.length > 0) {
    for (const item of listaCompra.value) {
      if (item["producto"].id === juego.id) {
        isInShop = true;
        break;
      } else {
        isInShop = false;
      }
    }
  }

  return isInShop;

}
const toggleFavorito = (producto_id) => {
  //document.getElementById('iconoFav').classList.toggle('text-red-600');

  if(document.getElementById('iconoFav').classList.contains('text-red-600')){
    deleteFromWishList(producto_id);
    document.getElementById('iconoFav').classList.toggle('text-white')}
  else{
    addToWishList(producto_id);
    document.getElementById('iconoFav').classList.toggle('text-red-600');
  }
} 

const addToWishList = async (producto_id) => {
  const data = {
    id_usuario: JSON.parse(localStorage.getItem('usuario')).id,
    id_producto: producto_id
  };
  try {
    const response = await axios.post(`listadeseo`, data);
    // console.log(response.data);
    console.log("Añadido a favoritos");
    //toggleFavorito();
  } catch (error) {
    console.error(error);
  }
};

const deleteFromWishList = async (producto_id) => {
  const data = {
    id_usuario: JSON.parse(localStorage.getItem('usuario')).id,
    id_producto: producto_id
  };
  try {
    const response = await axios.delete(`listadeseo/${data.id_usuario}/${data.id_producto}`);
    // console.log(response.data);
    console.log("Quitando de favoritos");

  } catch (error) {
    console.error(error);
  }
};

const getComentarios = () => {
  const path = `http://85.50.79.98:8080/resena/${juegoPasado.value.id}`;
  axios
    .get(path)
    .then((response) => {
      listaComentarios.value = response.data;
      console.log(listaComentarios.value);
    })
    .catch((error) => {
      console.error(error);
    });
};

const prevPage = () => {
  if(window.innerWidth <= 450){
    init.value -= 1;
    end.value -= 1;
    currentPage.value--;
  }else if((window.innerWidth > 450) && (window.innerWidth <= 700)){
    init.value -= 2;
    end.value -= 2;
    currentPage.value--;
  }else{
    init.value -= 3;
    end.value -= 3;
    currentPage.value--;
  }
};

const nextPage = () => {
  if(window.innerWidth <= 450){
    init.value += 1;
    end.value += 1;
    currentPage.value++;
  }else if((window.innerWidth > 450) && (window.innerWidth <= 700)){
    init.value += 2;
    end.value += 2;
    currentPage.value++;
  }else{
    init.value += 3;
    end.value += 3;
    currentPage.value++;
  }
};

// Show button for user loggin and user no loggin
var showButtonHeart = true;

function goToLogin(){
  router.push('/login');
}
onMounted(() => {
  if (store.state.juegoDetalle) {
    // console.log(store.state.datosCompartidos)
    juegoPasado.value = store.state.juegoDetalle;
    // console.log(juegoPasado.value)
  }

  if (Object.keys(juegoPasado.value).length === 0) {
    juegoPasado.value = JSON.parse(localStorage.getItem("juegoDetalle"));
  }

  const idUser = JSON.parse(localStorage.getItem("usuario"))?.id;
  if (idUser !== undefined) {
    const path = `http://85.50.79.98:8080/carrocompra?id_usuario=${idUser}`;
    comment.id_usuario = idUser;
    axios
      .get(path)
      .then((response) => {
        listaCompra.value = response.data;
        // console.log(listaCompra.value)
      })
      .catch((error) => {
        console.error(error);
      });
  }
  getComentarios();
  totalPages.value = Math.ceil(
    listaComentarios.value.length / productsPerPage.value
  );
  if (idUser !== undefined ){
    showButtonHeart = false;
  }else{
    showButtonHeart = true;
  }

  if(window.innerWidth <= 450){
    end.value = 1
    productsPerPage.value = 1
  }else if((window.innerWidth > 450) && (window.innerWidth <= 700)){
    end.value = 2;
    productsPerPage.value = 2
  }
});

//RUTA IMAGEN ../assets/jokin/{id}/1.webp
//LONGITUD MAXIMA DE DESCRIPCION {200} a partir de esta debe aparecer leer más

// descripcion: "Descripción de Half Life"
// id: 4
// id_descuento: 4
// id_plataforma: 4
// iframetrailer: "https://www.youtube.com/watch?v=O2W0N3uKXmo"
// precio_unitario: "19.99"
// producto: "Half Life"
// rutavideo: "/imgs/4/1.webm"
</script>

<template>
  <Navbar />
  <div class="md:mx-20 mx-9">
    <div>
      <div>
        <!--Seccion de foto del juego-->
      </div>
      <div>
        <!--Al lado div que contiene NOMBRE, PLATAFORMA, PRECIO, AÑADIR CARRITO, -->
      </div>
    </div>

    <div class="md:grid md:grid-cols-9 md:gap-4 mb-1 flex flex-col">
      <!--Debajo ACERCA DEL JUEGO-->
      <div class="md:col-span-6 mb-5">
        <!--Imagen-->
        <picture>
          <img
            alt="Imagen del juego"
            class="picture rounded-xl h-full"
            :src="getImgURL(juegoPasado.id)"
            loading="lazy"
          />
        </picture>
      </div>
      <div
        class="col-span-3 flex flex-col justify-between bg-background rounded-xl"
      >
        <div class="flex flex-col p-2">
          <h1 class="text-white md:mt-5 text-4xl text-center hidden md:block">Jokoari buruz</h1>
          <p class="text-white md:mt-5 mb-5 text-center hidden md:block">
            {{
              showAllDesc === true ? juegoPasado.descripcion : splitDescripcion
            }}
          </p>
          <!--Descripcion cortada-->
          <p
            v-show="showMore"
            @click="showMoreText"
            class="text-white hover:underline cursor-pointer text-center hidden md:block"
          >
            Irakurri gehiago...
          </p>
        </div>
        <div class="bg-background p-2 md:mt-2 rounded-xl">
          <!--NOMBRE-->
          <h1 class="text-white text-center text-4xl md:mt-5">
            {{ juegoPasado.producto }}
          </h1>
          <!--PLATAFORMA-->
          <div class="mt-2 flex flex-row justify-center align-middle">
            <h3 class="text-white text-center my-auto font-bold uppercase mr-2">
              Plataforma:
            </h3>
              <img
                :src="getLogoURL(juegoPasado.nombreplataforma)"
                alt="Imagen de la plataforma"
                class="w-10"
              />
              <h4 class="text-white text-center my-auto ml-3">
                {{ juegoPasado.nombreplataforma }}
              </h4>
          </div>
          <!--PRECIO-->
          <div class="text-white flex flex-row justify-center mt-2">
            <h3 class="text-center uppercase my-auto font-bold mr-2">
              Prezioa:
            </h3>
            <h3 class="text-center">{{ juegoPasado.precio_unitario }} €</h3>
          </div>
          <!--ADD TO CART BTN-->
          <div class="flex flex-row justify-center align-middle">
            <button
              aria-label="Add to cart"
              class="text-white font-black bg-resaltar p-2 mt-1 w-full rounded-lg"
              @click="addItemToCart(juegoPasado)"
            >
              <font-awesome-icon icon="shopping-cart" />
              Produktua erantsi
            </button>
            <button
              aria-label="Favorito"
              v-if="showButtonHeart"
              @click="goToLogin"
              class="text-white text-2xl ml-3 mr-3 rounded-xl"
            >
              <font-awesome-icon id="iconoFav" icon="heart" class="text-white"/>
            </button>
            <button
              aria-label="Favorito"
              v-else
              @click="toggleFavorito(juegoPasado.id)"
              class="text-white text-2xl ml-3 mr-3 rounded-xl"
            >
              <font-awesome-icon id="iconoFav" icon="heart" :class="{'text-red-600': juegoPasado.favorito}"/>
            </button>
          </div>
        </div>
      </div>
    </div>
    <div>
      <!--REVIEWS que se pueda comentar-->
      <div class="mt-5 bg-background rounded-xl mb-2">
        <!--Colocar aqui el metodo al enviarse el comentario-->
        <form @submit="sendComment" class="p-2">
          <div class="flex flex-row">
            <input
              required
              class="mx-4 mt-4 w-1/2 border-b-2 border-r-0 border-t-0 border-l-0 text-lg text-white  bg-background active:border-none "
              type="text"
              placeholder="Erreseina izenburua..."
              v-model="comment.resena"
            />
            <div class="flex flex-row ml-5 items-center align-middle">
              <font-awesome-icon icon="star" class="h-10 text-yellow-300"/>
              <input
                required
                class="mx-4 mt-4 w-1/2 border-b-2 border-r-0 border-t-0 border-l-0 text-lg text-white  bg-background active:border-none "

                type="number"
                placeholder="⭐"
                min="1"
                max="5"
                v-model="comment.valoracion"
              />
            </div>
          </div>
          <div class="flex flex-row gap-2 rounded-xl">
            <textarea
              required
              v-model="comment.contenido"
              rows="1"
              class="mx-4 mt-4 mb-10 w-full border-b-2 border-r-0 border-t-0 border-l-0 text-lg text-white  bg-background active:border-none "
              placeholder="Komentatu..."
            ></textarea>
          </div>
          <button 
            aria-label="Enviar comentario"
            class="text-white font-black bg-resaltar p-2 mt-1 w-full md:w-1/4 md:m-4 rounded-lg">
              Komentatu
            </button>
        </form>
      </div>
    </div>
  </div>
  <div v-show="listaComentarios.length > 0" class="flex flex-col mt-10 md:mx-20 mx-9">
    <!--PAGINATION-->
    <div class="flex flex-row justify-center gap-5">
      <h1 v-show="init !== 0" 
      class="text-white md:flex items-center justify-center uppercase font-bold hidden">Aurrekoa</h1>
      <button
        aria-label="Anterior"
        v-show="init !== 0"
        class="text-white bg-red-500 rounded-full p-5 w-10 h-10 flex items-center justify-center"
        @click="prevPage"
      >
        <font-awesome-icon icon="chevron-left" />
      </button>

      <button
        aria-label="Siguiente"
        v-show="
          currentPage < Math.ceil(listaComentarios.length / productsPerPage)
        "
        class="text-white bg-resaltar rounded-full p-5 w-10 h-10 flex items-center justify-center"
        @click="nextPage"
      >
        <font-awesome-icon icon="chevron-right" />
      </button>
      <h1
        v-show="
          currentPage < Math.ceil(listaComentarios.length / productsPerPage)
        "
        class="text-white md:flex items-center justify-center uppercase font-bold hidden"
      >
        Hurrengoa
      </h1>
    </div>
    <h1 class="text-gray-200 md:text-4xl text-2xl mt-5 font-barlow">
      Komentarioak
      <font-awesome-icon icon="chevron-right" />
    </h1>
    <div class="md:grid md:grid-cols-3 flex flex-row flex-nowrap gap-10">
      <Comentario
        v-for="(comment, index) in listaComentarios.slice(init, end)"
        :key="index"
        :comentario="comment"
      />
    </div>
  </div>
</template>
