<script setup>
import { reactive } from 'vue'
import axios from 'axios';
import { useRouter } from 'vue-router';


const router = useRouter()
const email = reactive({
  email: "",
  title: "",
  content: "",
})

const sendEmail = async () => {
  try {
    let data = JSON.stringify(email);

    let config = {
      method: 'post',
      maxBodyLength: Infinity,
      url: 'https://api.donostipub.eus/contacto',
      headers: {
        'Content-Type': 'application/json',
      },
      data: data,
    };

    
    const response = await axios.request(config);

    //console.log(JSON.stringify(response.data));

    router.push('/');


    Object.assign(email, {
      email: "",
      title: "",
      content: "",
    });
  } catch (error) {
    console.error(error);
  }
};

</script>

<template>
  <div class="dark">
    <section class="bg-white dark:bg-gray-900">
      <div class="py-8 lg:py-16 px-4 mx-auto max-w-screen-md">
        <h2
          class="mb-4 text-4xl tracking-tight font-extrabold text-center text-gray-900 dark:text-white"
        >
          Kontaktua
        </h2>
        <p
          class="mb-8 lg:mb-16 font-light text-center text-gray-500 dark:text-gray-400 sm:text-xl"
        >
          Arazo tekniko bat duzu? Zure iritzia bidali nahi diguzu gure
          orrialdeaz? Utziguzu hemendik jakiten.
        </p>
        <form class="space-y-8" @submit.prevent="sendEmail">
          <div>
            <label
              for="email"
              class="block mb-2 text-sm font-medium text-gray-900 dark:text-gray-300"
              >Zure posta elektronikoa</label
            >
            <input
              type="email"
              id="email"
              class="shadow-sm bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-primary-500 focus:border-primary-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500 dark:shadow-sm-light"
              placeholder="name@flowbite.com"
              v-model="email.email"
              required
            />
          </div>
          <div>
            <label
              for="subject"
              class="block mb-2 text-sm font-medium text-gray-900 dark:text-gray-300"
              >Izenburua</label
            >
            <input
              type="text"
              id="subject"
              class="block p-3 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 shadow-sm focus:ring-primary-500 focus:border-primary-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500 dark:shadow-sm-light"
              placeholder="Utzi jakiten nola lagun zaitzakegun"
              v-model="email.title"
              required
            />
          </div>
          <div class="sm:col-span-2">
            <label
              for="message"
              class="block mb-2 text-sm font-medium text-gray-900 dark:text-gray-400"
              >Zure mezua</label
            >
            <textarea
              id="message"
              rows="6"
              class="block p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg shadow-sm border border-gray-300 focus:ring-primary-500 focus:border-primary-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500"
              placeholder="Iruzkin bat idatzi..."
              v-model="email.content"
            ></textarea>
          </div>
          <button
            type="submit"
            class="py-3 px-5 text-sm font-medium text-center text-white rounded-lg bg-primary-700 sm:w-fit hover:bg-primary-800 focus:ring-4 focus:outline-none focus:ring-primary-300 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-primary-800"
          >
            Bidali mezua
          </button>
        </form>
      </div>
    </section>
  </div>
</template>
