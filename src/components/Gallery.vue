<script setup>
import axios from "axios";
import { ref, onMounted, computed } from "vue";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import { API_URL } from "../config";

const props = defineProps({
  title: String,
  required: true,
  games: Array,
  required: false,
});

/*
  isHovered -> Aplica a todos
  isHovered -> [1,1,1,1,1,1,1,1,1]

  index -> imagen
  index -> 3
  isHovered -> [1,1,0,1,1,1,1,1,1]
*/

//DATA
const isHovered = ref([]);
const video = ref([]);
const juegos = ref([]);
const router = useRouter();
const store = useStore();

//METHODS
const playVideo = (id) => {
  // console.log("video.value:", video.value);
  const playVideo = video.value[id].play();
  isHovered.value[id] = 0;
  // console.log(isHovered.value)
  if (playVideo !== undefined) {
    playVideo.then((_) => {}).catch((error) => {});
  }
};

const stopVideo = (id) => {
  const pauseVideo = video.value[id].pause();
  isHovered.value[id] = 1;

  if (pauseVideo !== undefined) {
    pauseVideo.then((_) => {}).catch((error) => {});
  }
};
// Funciton FOR TENDENCIAS, RESERVAS, MAS VENDIDOS
const handleJuegosResponse = (data) => {
  if (props.title === "Joerak") {
    juegos.value = data.slice(0, 9).map((object) => ({ ...object }));
  } else if (props.title === "Erreserbak") {
    juegos.value = data.slice(10, 19).map((object) => ({ ...object }));
  } else if (props.title === "Salduenak") {
    juegos.value = data.slice(20, 29).map((object) => ({ ...object }));
  }

  for (const i of data) {
    isHovered.value.push(1);
  }
};
const getJuegosNoUser = () => {
  const path = API_URL.concat("/all_productos");
  axios
    .get(path)
    .then((response) => {
      handleJuegosResponse(response.data);
    })
    .catch((error) => {
      console.error(error);
    });
};

const getJuegosUser = () => {
  const id_usuario = JSON.parse(localStorage.getItem("usuario")).id;
  const path = API_URL.concat(
    `/all_productos_usuario?id_usuario=${id_usuario}`
  );
  axios
    .get(path)
    .then((response) => {
      handleJuegosResponse(response.data);
    })
    .catch((error) => {
      console.error(error);
    });
};

const getJuegos = () => {
  try {
    getJuegosUser();
  } catch (error) {
    getJuegosNoUser();
  }
  // const id_usuario = JSON.parse(localStorage.getItem('usuario')).id;
  // if(id_usuario === undefined || id_usuario === null){

  // }else{

  // }
};

const getImageURL = (id) => {
  return `/imgs/juegos/${id}/2.webp`;
};

const getVideoURL = (id) => {
  // console.log(`/imgs/juegos/${id}/1.webm`)
  return `/imgs/juegos/${id}/1.webm`;
};

const sendGameDetails = (juego) => {
  store.commit("setJuegoDetalle", juego);
  localStorage.setItem("juegoDetalle", JSON.stringify(juego));
  // console.log(juego)
  router.push("/juegoDetalle");
};

//HOOKS
onMounted(() => {
  getJuegos();
  // console.log(video.value)
});
</script>

<template>
  <div class="flex flex-col mt-5 md:mt-10 md:ml-10 mx-10">
    <h1 class="flex flex-row text-gray-200 text-2xl md:text-4xl font-barlow gap-2">
      {{ title }}
      <font-awesome-icon icon="chevron-right" class="hidden md:flex" />
    </h1>

    <!-- const juego = reactive({
    id: 18,
    nombre: 'God Of War',
    precio: 45.78,
    plataformaId: 'Steam',
    iframeTrailer: '',
    descripcion: 'Lorem ipsum dolor sit, amet consectetur adipisicing elit. Reprehenderit beatae quae eius esse deserunt distinctio nulla. Modi reiciendis eius optio necessitatibus culpa voluptatibus magnam enim odio alias, quam doloribus ab?',
}) -->

    <div v-if="games === undefined" class="grid grid-cols-1 mt-5 gap-2 md:grid-cols-3 md:gap-7 md:mr-10 md:mt-10">
      <div
        v-for="(juego, index) in juegos"
        :key="juego.id"
        class="relative hover:scale-105 cursor-pointer transition-all duration-300"
        @click="sendGameDetails(juego)"
        @mouseover="playVideo(index)"
        @mouseout="stopVideo(index)"
      >
        <picture v-show="isHovered[index] === 1">
          <img
            class="picture rounded-xl"
            :src="getImageURL(juego.id)"
            loading="lazy"
            :alt="juego.producto + ' image'"
          />
        </picture>
        <video
          v-show="isHovered[index] !== 1"
          ref="video"
          preload="none"
          loop=""
          muted=""
          playsinline=""
          class="w-full h-full object-cover rounded-xl"
        >
          <source :src="getVideoURL(juego.id)" type="video/webm" />
        </video>
        <div
          class="flex justify-between mt-3 transition-opacity duration-300 ease-in-out"
          :class="{
            'opacity-0': isHovered[index] === 0,
            'opacity-100': isHovered[index] === 1,
          }"
        >
          <h1 class="text-white text-xs md:text-sm">{{ juego.producto }}</h1>
          <h2 class="text-white text-xs md:text-sm">{{ juego.precio_unitario }}€</h2>
        </div>
      </div>
    </div>
    <!--Si no funciona el de arriba colocar este-->
    <div v-else class="grid grid-cols-1 mt-5 gap-2 md:grid-cols-3 md:gap-7 md:mr-10 md:mt-10">
      <div
        v-for="(juego, index) in games"
        :key="juego.id"
        class="relative hover:scale-105 cursor-pointer transition-all duration-300"
        @click="sendGameDetails(juego)"
        @mouseover="playVideo(index)"
        @mouseout="stopVideo(index)"
      >
        <picture v-show="isHovered[index] === 1">
          <img
            class="picture rounded-xl"
            :src="getImageURL(juego.id)"
            loading="lazy"
          />
        </picture>
        <video
          v-show="isHovered[index] !== 1"
          ref="video"
          preload="none"
          loop=""
          muted=""
          playsinline=""
          class="w-full h-full object-cover rounded-xl"
        >
          <source :src="getVideoURL(juego.id)" type="video/webm" />
        </video>
        <div
          class="flex justify-between mt-3 transition-opacity duration-300 ease-in-out"
          :class="{
            'opacity-0': isHovered[index] === 0,
            'opacity-100': isHovered[index] === 1,
          }"
        >
          <h2 class="text-white text-xs md:text-sm">{{ juego.producto }}</h2>
          <h3 class="text-white text-xs md:text-sm">{{ juego.precio_unitario }}€</h3>
        </div>
      </div>
    </div>
  </div>
</template>
