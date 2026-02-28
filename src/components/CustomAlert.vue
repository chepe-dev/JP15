<script setup>
const props = defineProps({
  show: {
    type: Boolean,
    required: true
  },
  title: {
    type: String,
    default: 'Atención'
  },
  message: {
    type: String,
    required: true
  },
  type: {
    type: String,
    default: 'warning' // Puede ser 'warning' o 'success'
  }
});

const emit = defineEmits(['close']);
</script>

<template>
  <Transition name="fade">
    <div v-if="show" class="alert-overlay" @click.self="emit('close')">
      <div class="alert-modal">
        
        <div :class="['alert-header', type]">
          <h3 class="alert-title">{{ title }}</h3>
        </div>

        <div class="alert-body">
          <p class="alert-message">{{ message }}</p>
          <button class="alert-btn" @click="emit('close')">ENTENDIDO</button>
        </div>

      </div>
    </div>
  </Transition>
</template>

<style scoped>
/* Fondo oscuro translúcido */
.alert-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2000; /* Por encima de todo, incluso del navbar */
  padding: 1rem;
  box-sizing: border-box;
}

/* La caja de la alerta */
.alert-modal {
  background-color: white;
  width: 100%;
  max-width: 400px;
  border-radius: 12px;
  overflow: hidden; /* Para que el header respete los bordes redondos */
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  text-align: center;
}

/* Tipos de encabezado */
.alert-header {
  padding: 1rem;
  color: white;
}

.alert-header.warning {
  background-color: #E51C24; /* Rojo JP para advertencias o errores */
}

.alert-header.success {
  background-color: #5CBE12; /* Verde para mensajes de éxito (¡Felicidades!) */
}

.alert-title {
  margin: 0;
  font-size: clamp(1.1rem, 4vw, 1.3rem);
  font-weight: bold;
}

/* Cuerpo y mensaje */
.alert-body {
  padding: 1.5rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
}

.alert-message {
  margin: 0;
  font-size: clamp(0.9rem, 3vw, 1rem);
  color: #333;
  line-height: 1.5;
}

/* Botón de acción */
.alert-btn {
  background-color: black;
  color: white;
  border: none;
  padding: 0.75rem 2rem;
  font-weight: bold;
  font-size: 1rem;
  border-radius: 4px; /* Estilo píldora moderno */
  cursor: pointer;
  transition: all 0.2s ease;
  width: 100%;
}

.alert-btn:hover {
  background-color: #333;
  transform: translateY(-2px);
}

/* Animación Vue (Fade) */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>