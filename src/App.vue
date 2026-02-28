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
  <nav class="navbar">
    <div class="nav-content">
      <h1 class="nav-title">CÉDULA DE VOTACIÓN DIGITAL - TUTORIAL</h1>
      <p class="nav-subtitle">¿No sabes cómo votar por JP? Pruébalo aquí</p>
    </div>
    <img src="./assets/Logo_juntos_por_el_Peru.svg" alt="Logo JP" class="nav-logo" />
  </nav>

  <main class="main-container">
    
    <div v-if="!mostrarTabla" class="entry">
      <div class="vote-message">
        <h2>VOTA POR JUNTOS POR EL PERÚ</h2>
        <img src="./assets/Logo_juntos_por_el_Peru.svg" class="main-logo" alt="Logo Juntos por el Perú"/>
      </div>
      
      <button id="btnBeginVote" @click="iniciarVoto">Iniciar voto</button>
      
      <h3>Simulación para el distrito electoral de Áncash</h3>

      <img src="./assets/1ERA IMAGEN CEDULA DE VOTACION.png" alt="Cédula de Votación Áncash" class="ballot-image" />

    </div>

    <div v-else id="vote-page">
      <VoteTable />
    </div>

  </main>
</template>

<style scoped>
/* =========================================
   1. NAVBAR (100% Ancho y Responsivo)
========================================= */
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  background-color: #E51C24;
  color: white;
  display: flex;
  align-items: center;
  justify-content: space-between;
  
  /* Padding dinámico: se achica en celulares y crece en PC */
  padding: 1rem clamp(1rem, 5vw, 2rem); 
  box-sizing: border-box;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

.nav-content {
  display: flex;
  flex-direction: column;
  max-width: 80%; /* Evita que el texto empuje al logo en pantallas chicas */
}

.nav-title {
  margin: 0;
  /* El título fluye entre 0.9rem (móvil) y 1.2rem (PC) */
  font-size: clamp(0.9rem, 3vw, 1.2rem); 
  font-weight: bold;
}

.nav-subtitle {
  margin: 0;
  margin-top: 0.2rem;
  font-size: clamp(0.7rem, 2vw, 0.9rem);
  opacity: 0.9;
}

.nav-logo {
  /* Logo del nav responsivo */
  width: clamp(40px, 8vw, 50px);
  height: clamp(40px, 8vw, 50px);
  background-color: white;
  border-radius: 50%;
  padding: 0.25rem;
  object-fit: contain;
}

/* =========================================
   2. CONTENEDOR PRINCIPAL
========================================= */
.main-container {
  padding-top: 90px; /* Espacio para que el navbar no tape el contenido */
  min-height: 100vh; 
  display: flex;
  flex-direction: column;
  align-items: center;
  
  /* Márgenes laterales obligatorios para celulares */
  padding-left: 1rem;
  padding-right: 1rem;
  padding-bottom: 2rem;
  box-sizing: border-box;
}

/* =========================================
   3. PANTALLA DE ENTRADA (Entry)
========================================= */
.entry {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: clamp(1rem, 3vw, 2rem);
  text-align: center;
  width: 100%;
  max-width: 800px; /* Limita el ancho total en monitores grandes */
}

.vote-message {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.vote-message h2 {
  font-size: clamp(1.5rem, 5vw, 2.2rem);
  margin: 0;
  color: #333;
}

.main-logo {
  width: clamp(80px, 15vw, 120px);
  height: auto;
}

.entry h3 {
  font-size: clamp(1rem, 3vw, 1.3rem);
  color: #666;
  margin: 0;
  font-weight: normal;
}

/* =========================================
   4. IMAGEN DE LA CÉDULA (La gran corrección)
========================================= */
.ballot-image {
  width: 100%; /* Ocupa todo el espacio de su contenedor padre */
  max-width: 800px; /* NUNCA crecerá más allá de 600px en monitores */
  height: auto; /* Mantiene la proporción original para no deformarse */
  border-radius: 8px; /* Bordes ligeramente redondeados */
  box-shadow: 0 10px 20px rgba(0,0,0,0.15); /* Sombra elegante */
  margin-top: 1rem;
}

/* =========================================
   5. BOTÓN DE INICIO
========================================= */
#btnBeginVote {
  font-size: clamp(1.1rem, 4vw, 1.4rem);
  font-weight: bold;
  border: 3px solid black;
  background-color: white;
  color: black;
  width: 100%;
  max-width: 300px;
  padding: 1rem;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

#btnBeginVote:hover {
  color: white;
  background-color: #5CBE12;
  border-color: #5CBE12;
  transform: translateY(-2px);
}

/* =========================================
   6. CONTENEDOR DE LA TABLA
========================================= */
#vote-page {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* =========================================
   7. MEDIA QUERIES (Ajustes extra para pantallas miniatura)
========================================= */
@media (max-width: 400px) {
  .navbar {
    padding: 1rem 0.5rem; /* Menos espacio a los lados */
  }
}
</style>