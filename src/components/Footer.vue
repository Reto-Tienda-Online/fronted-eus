<script>
import creativeC from "@src/img/creativeC.svg";
import dinahosting from "@src/img/Dinahosting.png";
import eus from "@src/img/Puntueus-logoa.png";

export default {
  data() {
    return {
      creativeC: creativeC,
      dinahosting: dinahosting,
      eus: eus,
    };
  },
};
</script>

<template>
  <footer class="bg-gray-800 shadow dark:bg-gray-900 mt-10">
    <div class="w-full max-w-screen-xl mx-auto p-4 md:py-8">
      <div class="sm:flex sm:items-center sm:justify-between">
        <a
          href="https://flowbite.com/"
          class="flex items-center mb-4 sm:mb-0 space-x-3 rtl:space-x-reverse"
        >
          <img
            src="../img/icono-white.svg"
            class="w-[100px] h-[50px]"
            alt="Game Grover Logo"
          />
        </a>
        <ul
          class="flex flex-wrap items-center mb-6 text-sm font-medium text-gray-300 sm:mb-0 dark:text-gray-400"
        >
          <li>
            <a href="/#tendencias" class="hover:underline me-4 md:me-6"
              >Joerak</a
            >
          </li>
          <li>
            <a href="/#reservas" class="hover:underline me-4 md:me-6"
              >Erreserbak</a
            >
          </li>
          <li>
            <a href="/#bestSellers" class="hover:underline me-4 md:me-6"
              >Salduagoak</a
            >
          </li>
          <li>
            <a href="/contact-us" class="hover:underline">Kontaktua</a>
          </li>
        </ul>
      </div>
      <hr
        class="my-6 border-gray-200 sm:mx-auto dark:border-gray-700 lg:my-8"
      />

      <div class="sm:flex sm:items-center sm:justify-between">
        <span
          class="flex flex-row items-center text-sm text-gray-300 sm:text-center dark:text-gray-400"
          ><img
            :src="creativeC"
            alt="Creative Commons"
            width="35px"
            height="35px"
            class="text-white"
            loading="lazy"
          /><font-awesome-icon
            icon="person"
            aria-label="Creative Commons User"
            class="text-white px-2 py-1 ml-1 rounded-full border-4 text-lg font-bold"
          />
        </span>
        <div class="flex flex-col justify-center items-center">
          <img
            :src="eus"
            alt="Puntueus-logoa"
            loading="lazy"
            width="100px"
            height="100px"
          />
          <img
            :src="dinahosting"
            alt="Dinahiosting logoa"
            loading="lazy"
            width="200px"
            height="200px"
          />
        </div>
      </div>
    </div>
    <div class="flex w-auto flex-col mt-0 justify-center">
      <p class="text-white font-medium md:font-semibold text-center text-balance md:text-pretty text-xs md:text-sm">
        All submitted content remains copyright its original copyright holder.
      </p>
      <p class="mt-2 text-white font-medium md:font-semibold text-center text-balance md:text-pretty text-xs md:text-sm">
        DMCA Copyright Violation Images are for personal, non commercial use.
      </p>
    </div>
  </footer>
</template>
