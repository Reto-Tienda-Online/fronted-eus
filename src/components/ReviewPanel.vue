<script setup>
import axios from "axios";
import { reactive, onMounted, ref } from "vue";

/*{
    "id": 23,
    "resena": "Reseña para usuario Diego",
    "id_usuario": 9,
    "contenido": "Hellow",
    "id_juego": 1,
    "valoracion": 5
  } */
const bestComment = reactive({
  id: 0,
  resena: "",
  id_usuario: 0,
  contenido: "",
  id_juego: 0,
  valoracion: 1,
});

const gameName = ref("")

const getBestComment = () => {
  const path = "https://api.donostipub.eus/highest_valoracion/18";
  axios
    .get(path)
    .then((response) => {
      Object.assign(bestComment, response.data);
      // //console.log(bestComment.resena)
    })
    .catch((error) => {
      console.error(error);
    });
};

const getImgURL = () => {
  return `/imgs/juegos/${bestComment.id_juego}/1.webp`;
};

const getGameName = () => {
  const path = "https://api.donostipub.eus/all_productos?id=18"
  axios
    .get(path)
    .then((response) => {
      gameName.value = response.data[0].producto
    })
}

onMounted(() => {
  getBestComment();
  getGameName();
});
</script>

<template>
  <div class="text-white mt-20 flex flex-row justify-start bg-gray-800 bg-cover p-10">
    <img :src="getImgURL()" :alt="`Image ${gameName}`" class="md:w-1/3 md:-mt-20 md:-mb-10 md:relative md:block hidden" />
    <div class="flex flex-col justify-around text-center w-full">
      <h2 class="text-2xl mb-5 md:hidden">
        {{ gameName }}
      </h2>
      <h2 class="md:text-5xl text-2xl">
        {{ "⭐".repeat(bestComment.valoracion) }}
      </h2> 
      <q class="md:text-xl md:-mt-10 mt-5 font-bold italic text-pretty">
        {{ bestComment.comentario }}
      </q>
      <h2 class="md:-mt-8 mt-5 text-3xl font-bold">
        {{ bestComment.titulo }}
      </h2>
    </div>
  </div>
</template>
