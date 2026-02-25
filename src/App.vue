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
      <h1>Vota por</h1>
      <img src="./assets/Logo_juntos_por_el_Peru.svg" width="100px" height="100px"/>
      <h1>#15</h1>
    </div>
    <button id="btnBeginVote" @click="iniciarVoto">Iniciar voto</button>
  </div>

  <div v-else id="vote-page">
    <VoteTable />
  </div>
</template>