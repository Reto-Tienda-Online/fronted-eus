<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import { API_URL } from "../config";

const store = useStore();
const router = useRouter();
const categories = ref([]);

const getCategories = () => {
  const path = API_URL.concat("/all_categorias");
  axios
    .get(path)
    .then((response) => {
      categories.value = response.data;
      // console.log(categories.value);
    })
    .catch((error) => {
      console.error(error);
    });
    console.log(window.innerWidth)
};

const categoryPng = (categoria) => {
  return `/imgs/personajes/${categoria}/1.webp`;
};

const categoryBg = (categoria) => {
  return `/imgs/personajes/${categoria}/1_1.webp`;
};

const changeView = (category) => {
  store.commit("setCategoryData", category);
  router.push("/categoriaDetalle");
};



onMounted(() => {
  getCategories();
});
</script>

<template>
  <div class="flex flex-col mt-5 md:mt-10 md:ml-10 cursor-pointer mx-10">
    <h1 class="flex flex-row text-gray-200 text-2xl md:text-4xl font-barlow">
      Kategoriak
      <font-awesome-icon icon="chevron-right" class="hidden md:flex"/>
    </h1>
    <div class="grid grid-cols-1 md:grid-cols-3 md:gap-y-14 md:gap-x-7 md:mr-10 md:mt-14">
      <div
        v-for="(item, index) in categories"
        :key="item.id"
        style="position: relative"
        class="rounded-xl mt-10 md:mt-0"
        @click="changeView(item.categoria)"
      >
        <!--Las imagenes deben ser de todas las imagenes deben ser de 250px de altura da igual el ancho pero no mas de 250px -->
        <img
          :src="categoryBg(item.categoria)"
          alt="Imagen categoría"
          class="relative rounded-xl opacity-50 "
        />
        <h1
          class="absolute top-1/2 left-5 transform -translate-y-1/2 text-white font-barlow font-bold text-xs md:text-2xl"
        >
          {{ item.categoria }}
        </h1>
        <!--La imagen del personaje debe ser de s-->
        <img
          :src="categoryPng(item.categoria)"
          alt="Imagen categoría"
          class="absolute right-0 bottom-0 hover:scale-105 cursor-pointer transition-all duration-500 origin-bottom-right hidden md:block"
        />
        
        
      </div>
    </div>
  </div>
</template>
