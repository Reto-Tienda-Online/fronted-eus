<template>
  <nav class="w-[420px] md:w-full flex justify-between align-middle">
    <div class="flex flex-col justify-start">
      <img
        class="mt-5 ml-5 w-16 h-18 hover:cursor-pointer"
        src="../img/icono-white.svg"
        alt="Logotipo"
        @click="goHome"
      />
    </div>
    <ul
      class="w-[420px] md:w-full mt-5 flex flex-row justify-center align-middle space-x-5 text-gray-200 font-bold font-barlow"
    >
      <!-- Añadir button a cada font para sacar dialogo de las pc que contiene -->
      <li
        class="w-10 md:w-auto flex justify-center items-center font-barlow hover:text-gray-200 cursor-pointer"
      >
        <a
          href="/cesta"
          class="rounded-full border-2 md:border-4 px-3 py-1 border-primary"
          :class="{ 'border-resaltar': isActive('Cesta') }"
          >1</a
        >
        <p class="hidden md:block md:mx-2">Saskia</p>
      </li>

      <li
        class="w-10 md:w-auto flex justify-center items-center font-barlow hover:text-gray-200 cursor-pointer"
      >
        <span
          class="px-2 md:px-5 w-10 h-1 bg-primary"
          :class="{ 'bg-resaltar': isActive('Pago') }"
        ></span>
        <a
          href=""
          class="rounded-full border-2 md:border-4 px-3 py-1 border-primary"
          :class="{ 'border-resaltar': isActive('Pago') }"
          >2</a
        >
        <p class="hidden md:block md:mx-2">Ordainketa</p>
      </li>
      <li
        class="w-10 md:w-auto flex justify-center items-center font-barlow hover:text-gray-200 cursor-pointer"
      >
        <span
          class="px-2 md:px-5 h-1 md:w-10  bg-primary"
          :class="{ 'bg-resaltar': isActive('ActivarJuego') }"
        ></span>
        <a
          href="#"
          class="rounded-full border-2 md:border-4 px-3 py-1 border-primary"
          :class="{ 'border-resaltar': isActive('ActivarJuego') }"
          >3</a
        >
        <p class="hidden md:block md:mx-2">Jokoa aktibatu</p>
      </li>
    </ul>
    <ul class="w-10 md:w-auto flex flex-row text-gray-200 mt-5 mr-5 space-x-7">
      <!-- Agregar respectivos buttons para inicio de sesion y carro de compras -->
      <li>
        <font-awesome-icon
          icon="lock"
          class="text-2xl hover:text-resaltar cursor-pointer transition-all duration-500 ease-in-out"
        />
      </li>
    </ul>
  </nav>
</template>

<script setup>
import { useRouter } from "vue-router";

const router = useRouter();

const props = defineProps(["activeElement"]);

const isActive = (element) => element === props.activeElement;

const goHome = () => {
  router.push("/");
};
</script>
