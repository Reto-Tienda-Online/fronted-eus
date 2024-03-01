<script>
import creativeC from '@src/img/creativeC.svg'; // Assuming '@' is configured as alias for 'src' directory in your project

export default {
  data() {
    return {
      creativeC: creativeC
    };
  }
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
            <a href="#tendencias" class="hover:underline me-4 md:me-6"
              >Joerak</a
            >
          </li>
          <li>
            <a href="#reservas" class="hover:underline me-4 md:me-6"
              >Erreserbak</a
            >
          </li>
          <li>
            <a href="#bestSellers" class="hover:underline me-4 md:me-6"
              >Salduagoak</a
            >
          </li>
          <li>
            <a href="#" class="hover:underline">Kontaktua</a>
          </li>
        </ul>
      </div>
      <hr
        class="my-6 border-gray-200 sm:mx-auto dark:border-gray-700 lg:my-8"
      />
      <span
        class="flex flex-row items-center text-sm text-gray-300 sm:text-center dark:text-gray-400"
        ><img :src="creativeC" width="35px" height="35px" class="text-white"/><font-awesome-icon icon="person" class="text-white px-2 py-1 ml-1 rounded-full border-4 text-lg font-bold" />
      </span>
    </div>
  </footer>
</template>
