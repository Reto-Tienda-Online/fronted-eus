<template>
    <div>
        <NavBarCesta activeElement="Cesta"/>
        <CestaMain />
        <Footer/>
    </div>
</template>

<script setup>
//Imprescindible "script setup"
import NavBarCesta from '@components/NavBarCesta.vue'
import CestaMain from '@components/CestaMain.vue'
import Footer from '../components/Footer.vue';
</script>

