<script setup>
//Imprescindible "script setup"
import Navbar from "../components/Navbar.vue";
import Carousel from "../components/Carousel.vue";
import Footer from "../components/Footer.vue";
import ReviewPanel from "../components/ReviewPanel.vue";
import Gallery from "../components/Gallery.vue";
import Categories from "../components/Categories.vue";
import TrustPanel from "../components/TrustPanel.vue";
import CategoriaDetalle from "../components/CategoriaDetalle.vue";

/*
  TEMPLATE REAL

  <CategoriaDetalle/>
*/
</script>

<template>
  <Navbar />
  <div class="mt-10 text-center">
    <h1 class="font-black text-4xl text-gray-200 font-barlow">
      Online <span class="text-indigo-600">Joko </span>Denda
    </h1>
  </div>
  <Carousel />
  <div id="tendencias" class="md:mx-20">
    <Gallery title="Joerak" />
    <!--Lista de Juegos en Tendencia-->
  </div>
  <TrustPanel />
  <div id="reservas" class="md:mx-20">
    <Gallery title="Erreserbak"/>
    <!--Lista de Juegos Coming Soon-->
  </div>
  <ReviewPanel />
  <!--Pasar lista de comentarios positivos y coger uno random-->
  <!--Se podría poner mas comentarios pero esta en veremos-->
  <div id="bestSellers" class="md:mx-20">
    <Gallery title="Salduenak" />
  </div>
  <!--Colocar algo en medio aqui-->
  <div id="categories" class="md:mx-20">
    <Categories />
  </div>
  <Footer />
</template>
