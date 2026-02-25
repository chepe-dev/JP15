<script setup>
  import { ref } from 'vue';
  import VoteTable from './components/VoteTable.vue';

  // Creamos una variable reactiva para controlar qué se ve
  const mostrarTabla = ref(false);

  // Función para cambiar el estado
  const iniciarVoto = () => {
    mostrarTabla.value = true;
  };
</script>

<template>
  <div v-if="!mostrarTabla" class="entry">
    <div class="vote-message">
      <h1>VOTA POR JUNTOS POR EL PERÚ</h1>
      <img src="./assets/Logo_juntos_por_el_Peru.svg" width="100px" height="100px"/>
    </div>
    <button id="btnBeginVote" @click="iniciarVoto">Iniciar voto</button>
    <h2>Simulación para el distrito electoral de Áncash</h2>
  </div>

  <div v-else id="vote-page">
    <VoteTable />
  </div>
</template>