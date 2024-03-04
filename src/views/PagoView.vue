<template>
    <div>
        <NavBarCesta activeElement="Pago"/>
        <PagoMain />
        <Footer/>
    </div>
</template>

<script setup>
//Imprescindible "script setup"
import NavBarCesta from '@components/NavBarCesta.vue'
import PagoMain from '@components/PagoMain.vue'
import Footer from '../components/Footer.vue';
</script>

