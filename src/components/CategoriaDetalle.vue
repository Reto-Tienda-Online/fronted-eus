<script setup>
import { ref, onMounted, watch } from "vue";
import axios from "axios";
import { useStore } from "vuex";
import Navbar from "./Navbar.vue";
import Gallery from "./Gallery.vue";
import Footer from "./Footer.vue";
import { API_URL } from "../config";

const store = useStore();
const categories = ref([]);
const plataformas = ref([]);
const selectedCategory = ref("Aukeratu kategoria bat");
const selectedPlataform = ref("Aukeratu plataforma bat");
const searchInput = ref("");
const games = ref([]);

const getCategories = () => {
  const path = API_URL.concat("/all_categorias");
  axios
    .get(path)
    .then((response) => {
      categories.value = response.data;
    })
    .catch((error) => {
      console.log(error);
    });
};

const getPlataformas = () => {
  const path = "http://85.50.79.98:8080/all_plataformas";
  axios
    .get(path)
    .then((response) => {
      plataformas.value = response.data;
    })
    .catch((error) => {
      console.log(error);
    });
};

const getJuegos = (plataforma = "", categoria = "") => {
  const path = "http://85.50.79.98:8080/all_productos";
  axios
    .get(path)
    .then((response) => {
      games.value = response.data;
      if (plataforma !== "" && categoria === "") {
        // console.log(plataforma)
        // console.log("ENTRA")
        games.value = games.value.filter((game) => {
          return game.nombreplataforma === plataforma;
        });
        // console.log(games.value)
      } else if (plataforma !== "" && categoria !== "") {
        games.value = games.value.filter((game) => {
          return (
            game.nombreplataforma === plataforma &&
            game.nombrecategoria === categoria
          );
        });
      } else if (categoria !== "" && plataforma === "") {
        games.value = games.value.filter((game) => {
          return game.nombrecategoria === categoria;
        });
      }
    })
    .catch((error) => {
      console.log(error);
    });
};

const filterGames = () => {
  getJuegos(selectedPlataform.value, selectedCategory.value);
};

const filterByName = () => {
  games.value = games.value.filter((element) => {
    return element.producto
      .toLowerCase()
      .includes(searchInput.value.toLowerCase());
  });
};

const filterByNameBackspace = () => {
  const path = `http://85.50.79.98:8080/search?producto=${searchInput.value}`;
  axios
    .get(path)
    .then((response) => {
      filterGames();
      games.value = response.data;
    })
    .catch((error) => {
      console.error(error);
    });
};

onMounted(() => {
  getJuegos();
  getCategories();
  selectedCategory.value = store.state.categoryData;
  selectedPlataform.value = store.state.plataformData;

  //RESETEO LA PLATAFORMA
  store.commit("setPlataformData", "");
  store.commit("setCategoryData", "");
  getPlataformas();
  filterGames();
});
</script>

<template>
  <Navbar />
  <div
    class="flex flex-col md:flex-row mt-5 md:justify-center md:align-middle mx-10"
  >
    <div class="flex flex-col mr-10 flex-1">
      <label class="text-white text-left" for="nombreFiltro">
        Bilatu <font-awesome-icon icon="search" class="mr-2"
      /></label>
      <input
        class="text-black mt-5 rounded-md"
        placeholder="Jokoaren izena..."
        type="search"
        @input="filterByName"
        @keyup.delete="filterByNameBackspace"
        @search="filterGames"
        v-model="searchInput"
      />
    </div>
    <div class="flex mt-2 md:mt-0 flex-col flex-3">
      <label for="categoria" class="text-white text-left md:text-center">
        Kategoriak
      </label>
      <select
        id="categoria"
        class="text-black mt-5 rounded-md"
        @change="filterGames"
        v-model="selectedCategory"
      >
        <option value="">Aukeratu kategoria bat</option>
        <option
          v-for="(category, index) in categories"
          :key="category.id"
          :value="category.categoria"
        >
          {{ category.categoria }}
        </option>
      </select>
    </div>
    <div class="flex flex-col mt-2 md:mt-0 md:ml-10 flex-3">
      <label for="plataforma" class="text-white text-left md:text-center">
        Plataformak
      </label>
      <select
        id="plataforma"
        class="text-black mt-5 rounded-md"
        @change="filterGames"
        v-model="selectedPlataform"
      >
        <option value="" selected class="">Aukeratu plataforma bat</option>
        <option
          v-for="(plataforma, index) in plataformas"
          :key="plataforma.id"
          :value="plataforma.plataforma"
        >
          {{ plataforma.plataforma }}
        </option>
      </select>
    </div>
  </div>
  <Gallery :title="selectedCategory" :games="games" />
  <Footer />
</template>
