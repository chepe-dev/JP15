<script setup>
const props = defineProps({
  candidates: {
    type: Array,
    required: true
  }
});

const emit = defineEmits(['selectCandidate', 'cancel']);

const selectNumber = (number) => {
  emit('selectCandidate', number);
};
</script>

<template>
  <div class="modal-overlay" @click.self="emit('cancel')">
    <div class="modal-content">
      <h2>Elige tu candidato preferencial</h2>
      <p>Marca el número de tu preferencia</p>
      
      <div class="grid-candidates">
        <button
          class="candidate-card"
          @click="selectNumber(null)">
          <span class="candidate-photo x-mark">?</span>
          
          <div class="candidate-info">
            <span class="candidate-name">Voto en Blanco</span>
            <span class="candidate-number"># 0</span>
          </div>
        </button>
        <button 
          v-for="candidate in candidates" 
          :key="candidate.number"
          class="candidate-card"
          @click="selectNumber(candidate.number)"
        >
          <img 
            :src="candidate.photo" 
            :alt="candidate.name" 
            class="candidate-photo" 
          />
          
          <div class="candidate-info">
            <span class="candidate-name">{{ candidate.name }}</span>
            <span class="candidate-number"># {{ candidate.number }}</span>
          </div>
        </button>
      </div>

      <button class="btn-cancel" @click="emit('cancel')">Cancelar</button>
    </div>
  </div>
</template>

<style scoped>
.x-mark {
  font-size: 3rem;
  font-weight: bold;
  color: #000;
  font-family: Arial, sans-serif;
}


/* El fondo oscuro que opaca la tabla */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

/* Tarjeta blanca central */
.modal-content {
  background-color: white;
  padding: 2rem;
  text-align: center;
  width: 90%;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
  max-width: 500px; /* Un poco más ancho para que quepan las tarjetas */
  box-shadow: 0 4px 15px rgba(0,0,0,0.3);
}

/* Grilla de candidatos */
.grid-candidates {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1rem;
  margin: 1.5rem 0;
  overflow-y: auto;
  padding-right: 0.5rem;
  padding-top: 0.5rem;
}

/* Estilos de la nueva tarjeta de candidato */
.candidate-card {
  display: flex;
  border-radius: 4px;
  flex-direction: column;
  align-items: center;
  background-color: white;
  border: 2px solid #ccc;
  padding: 1rem;
  cursor: pointer;
  transition: all 0.2s ease;
}

/* Efecto Hover más moderno para imágenes */
.candidate-card:hover {
  border-color: #E51C24; /* Resalta el borde con el rojo de JP */
  background-color: #fcfcfc;
  transform: translateY(-3px); /* Efecto de que la tarjeta se levanta */
  box-shadow: 0 4px 10px rgba(229, 28, 36, 0.15);
}

.candidate-photo {
  width: 80px;
  height: 80px;
  object-fit: cover; /* Asegura que la foto no se deforme */
  border-radius: 50%; /* Hace la foto circular */
  border: 2px solid #ddd;
  margin-bottom: 0.75rem;
}

.candidate-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.25rem;
}

.candidate-name {
  font-size: 1rem;
  font-weight: bold;
  color: #333;
}

.candidate-number {
  font-size: 1.5rem;
  font-weight: 900;
  color: #E51C24; /* El número en rojo destaca más */
}

.btn-cancel {
  background-color: white;
  color: black;
  border: 2px solid black;
  padding: 0.5rem;
  cursor: pointer;
  font-weight: bold;
  border-radius: 4px;
}

.btn-cancel:hover {
  background-color: black;
  color: white;
  transition: 0.35s;
}
</style>