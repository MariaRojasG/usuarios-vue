<script setup>
// 1. PROPS: Datos de solo lectura que recibimos del Padre (App.vue)
defineProps({
  nombre: String,
  email: String,
  esFavorito: Boolean
});

// 2. EMITS: Eventos que podemos "gritar" hacia el Padre
const emit = defineEmits(['seleccionar', 'toggle-favorito']);
</script>

<template>
  <div class="card" @click="emit('seleccionar')">
    <h3>{{ nombre }}</h3>
    <p>{{ email }}</p>

    <!-- @click.stop evita que el clic dispare también el de la tarjeta -->
    <button @click.stop="emit('toggle-favorito')">
      {{ esFavorito ? '❌ Quitar favorito' : '⭐ Favorito' }}
    </button>
  </div>
</template>

<style scoped>
.card {
  border: 1px solid #ccc;
  padding: 12px;
  margin: 10px 0;
  cursor: pointer;
  border-radius: 8px;
}
</style>