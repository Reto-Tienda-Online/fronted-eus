<script setup>
import ErrorModal from "../components/ErrorModal.vue";
import SuccessModal from "../components/SuccessModal.vue";
import { ref, onMounted, watchEffect } from "vue";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import axios from "axios";

const router = useRouter();
const store = useStore();
const TOKEN_TEST = "tok_mastercard";
const listaJuegos = ref([]);
const payResponse = ref(null);
const IVA = 1.21;
const cantidadIVA = ref(0);
const precioConIVA = ref(0);
const precioTotal = ref(0);

const stripeResponse = () => {
  let data = JSON.stringify({
    amount: Math.round(precioConIVA.value * 100),
    currency: "usd",
    token: TOKEN_TEST,
  });
  let config = {
    method: "post",
    maxBodyLength: Infinity,
    url: "https://api.donostipub.eus/process-payment/",
    headers: {
      "Content-Type": "application/json",
    },
    data: data,
  };
  axios
    .request(config)
    .then((response) => {
      //console.log(JSON.stringify(response.data));
      if (response.data.status === "success") {
        payForAll();
      }
    })
    .catch((error) => {
      //console.log(error);
      setTimeout(() => {
        payResponse.value = null;
        router.push("/");
      }, 3000);
    });
};

const payForAll = () => {
  const id_user = JSON.parse(localStorage.getItem("usuario")).id;
  //console.log(id_user);
  let config = {
    method: "post",
    maxBodyLength: Infinity,
    url: `https://api.donostipub.eus/process_transaction/${id_user}`,
    headers: {
      "Content-Type": "application/json",
    },
  };
  axios
    .request(config)
    .then((response) => {
      //console.log(JSON.stringify(response.data));
      if (response.data.result === "Transaction processed successfully") {
        payResponse.value = true;
      }

      setTimeout(() => {
        payResponse.value = null;
        router.push("/");
      }, 3000);
    })
    .catch((error) => {
      //console.log(error);
      payResponse.value = false;
      setTimeout(() => {
        payResponse.value = null;
        router.push("/");
      }, 3000);
    });
};

const calcularPrecioTotal = () => {
  for (const juego of listaJuegos.value) {
    //console.log(juego.cantidad);
    precioTotal.value +=
      parseFloat(juego["producto"].precio_unitario) * juego.cantidad;
  }

  cantidadIVA.value = precioTotal.value * 0.21;
};

const calcularIVA = () => {
  precioConIVA.value = precioTotal.value * IVA;
  // //console.log(precioConIVA.value + "PI")
};

onMounted(() => {
  const idUser = JSON.parse(localStorage.getItem("usuario")).id;
  const path = `https://api.donostipub.eus/carrocompra?id_usuario=${idUser}`;
  axios
    .get(path)
    .then((response) => {
      listaJuegos.value = response.data;
      // //console.log(listaJuegos.value)
    })
    .catch((error) => {
      console.error(error);
    });
});

watchEffect(() => {
  calcularPrecioTotal();
  calcularIVA();
});
</script>

<template>
  <div class="flex flex-row justify-center mt-5" v-if="payResponse">
    <SuccessModal />
  </div>
  <div
    class="flex flex-row justify-center mt-5"
    v-else-if="payResponse === false"
  >
    <ErrorModal />
  </div>

  <!--ESTA PARTE DEL CODIGO HASTA DONDE ESTA MARCADO NO SE IMPLEMENTA-->
  <main class="text-white flex flex-col md:flex-row justify-center w-auto h-auto">

    <form class="flex flex-col w-auto justify-center mx-4">
      <h2 class="md:text-xl text-base font-medium md:font-bold my-3">Fakturazio-helbidea</h2>
      <div class="flex flex-col md:flex-row md:items-center justify-center md:justify-between w-auto">
        <input
          class="bg-[#272727] rounded-md w-1/2 h-auto md:w-auto my-2 flex border-2 py-3 px-2 border-gray-400"
          type="text"
          placeholder="Izena"
        />
        <input
          class="bg-[#272727] rounded-md w-2/3 h-auto  md:w-auto flex border-2 md:ml-4 py-3 px-2 border-gray-400"
          type="text"
          placeholder="Fakturazio Helbidea"
        />
      </div>
      <label class="md:text-xl text-base font-medium md:font-bold my-3"> Herrialdea:
        <select
          class="bg-[#272727] rounded-md border-2 my-5 py-3 px-2 border-gray-400 w-auto"
        >
          <option>Singapore</option>
          <option>Luxembourg</option>
          <option>Italy</option>
          <option>Germany</option>
          <option>USA</option>
          <option>Canada</option>
          <option>Brazil</option>
          <option>UAE</option>
        </select>
      </label>
      <h2 class="text-base md:text-xl font-medium md:font-bold my-3 focus">Ordainketa-metodoak</h2>
      <div id="payment-method-93" class="rounded-md bg-[#272727] md:py-7 md:px-5 py-4 px-0 flex justify-center w-[fit-content] md:w-auto">
        <div
          class="flex text-sm md:text-base flex-row w-auto hover:border-resaltar active:border-resaltar focus:border-resaltar"
        >
          <input
            type="radio"
            name="payment-platform"
            value="PAYPAL"
            class="hidden"
          />
          <img
            class="rounded-lg w-12 h-10 md:w-24 md:h-20  px-2 md:px-0"
            src="https://brandemia.org/sites/default/files/sites/default/files/logo_paypal_principal.jpg"
            alt="Imagen de PayPal"
          />
          <div class="flex flex-col mx-5">
            <span class="md:font-bold font-semibold"
              >Paypal<span class="text-gray-100">(+0.20€)</span></span
            >
            <div class="paypal-agreement-container">
              <label class="checkbox"
                ><input type="checkbox" name="paypal_save" checked="checked" />
                <span class="checkmark"></span>
                Gorde zure hurrengo erosketarako
              </label>
            </div>
          </div>
        </div>
      </div>
    </form>
    
    <div class="w-[fit-content] flex flex-col mx-6 md:mx-14">
      <input id="pluginsdata" type="hidden" name="pluginsdata" value="" />
      <input type="hidden" name="process" value="pay" />
      <input
        type="hidden"
        name="usid"
        value="1a99ccc3ce502635ed64a38aeeda80291ca362ad7ea7db61f3a1ae74c85f18ea"
      />
      <!--HASTA AQUI NO SE IMPLEMENTA NADA A PARTIR DE AHORA SI -->

      <input type="hidden" name="ioBB" id="ioBB" />
      <h2 class="text-xl font-medium md:font-bold my-3">Laburpena</h2>
      <div class="flex flex-col ">
        <div class="bg-[#272727] py-4 px-3 md:py-8 md:px-7 rounded-lg">
          <div class="w-auto flex flex-col ">
            <div
              v-for="(juego, index) in listaJuegos"
              :key="juego['producto'].id"
              class="flex justify-between"
            >
              <div class="flex flex-col">
                <span class="name"
                  ><span
                    :title="juego['producto'].producto"
                    class="font-medium md:font-bold text-md md:text-lg"
                    >{{ juego["producto"].producto }}
                    <span class="text-resaltar" v-show="juego.cantidad > 1"
                      >({{ juego.cantidad }})
                    </span>
                  </span></span
                >
                <span class="text-xs">
                  {{ juego["producto"].plataforma.plataforma }}
                </span>
                <br />
              </div>
              <span class="text-gray-100"
                >{{ juego["producto"].precio_unitario }}€</span
              >
            </div>
            <span class="h-[0.5px] bg-gray-200 w-full"></span>
          </div>
          <!-- <div class="payment-gift">
            <small
              ><div class="icon-gift icon-xxs"></div>
              Print your game as a gift after validating your purchase
            </small>
          </div> -->
        </div>
        <div class="bg-[#272727] py-8 px-8 my-7 rounded-lg">
          <div class="flex justify-between">
            <span>Transakzio-kostuak</span>
            <span class="text-gray-100">0.20€</span>
          </div>
          <div id="ig-vat" data-rate="21.00" class="flex justify-between my-2">
            <span>IVA (<span>21.00</span>%) :</span>
            <span class="text-gray-100"
              ><span>{{ cantidadIVA.toFixed(2) }}€</span></span
            >
          </div>
          <div class="flex justify-between font-medium md:font-bold text-base md:text-xl my-4">
            <span class="texttotal">Guztira</span>
            <span class="subtotal">{{ precioConIVA.toFixed(2) }}€</span>
          </div>
          <div
            @click="stripeResponse"
            class="flex justify-center py-5 my-6 bg-resaltar rounded-lg hover:cursor-pointer"
          >
            <button type="submit" class="button default">Ordaindu</button>
          </div>
        </div>
      </div>
      <label class="text-xstext-gray-100 text-pretty"
        > "Ordaindu" sakatuz gero, <a class="underline" href="/es/terminos-y-condiciones/" target="_blank"
          >términos y condiciones</a
        > onartu ditudala aitortzen dut>.</label
      >
    </div>
  </main>
</template>
