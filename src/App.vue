<script setup>
import { ref, computed, onMounted } from 'vue';
import UsuarioCard from './components/UsuarioCard.vue';

// 1. CAJAS REACTIVAS (ref) - En JS se accede/modifica con .value
const usuarios = ref([]);
const busqueda = ref('');
const ciudadFiltro = ref('');
const cargando = ref(true);
const error = ref(null);
const usuarioSeleccionado = ref(null);
const favoritos = ref([]);
const darkMode = ref(false);

// 2. DESPERTADOR (onMounted) - Trae datos de la API una vez al iniciar
onMounted(async () => {
  try {
    cargando.value = true;
    const respuesta = await fetch('https://jsonplaceholder.typicode.com/users');
    usuarios.value = await respuesta.json();
  } catch (err) {
    error.value = 'Error al cargar usuarios';
  } finally {
    cargando.value = false;
  }
});

// 3. CALCULADORA AUTOMÁTICA (computed) - Se cachea y recalcula solo si cambian filtros
const usuariosFiltrados = computed(() => {
  return usuarios.value.filter((u) => {
    const coincideNombre = u.name.toLowerCase().includes(busqueda.value.toLowerCase());
    const coincideCiudad = ciudadFiltro.value === '' || u.address.city === ciudadFiltro.value;
    return coincideNombre && coincideCiudad;
  });
});

// 4. MÉTODOS PARA EVENTOS
function limpiarFiltros() {
  busqueda.value = '';
  ciudadFiltro.value = '';
}

function toggleFavorito(usuario) {
  const existe = favoritos.value.some((fav) => fav.id === usuario.id);
  if (existe) {
    favoritos.value = favoritos.value.filter((fav) => fav.id !== usuario.id);
  } else {
    favoritos.value.push(usuario);
  }
}
</script>

<template>
  <div :class="['contenedor', { oscuro: darkMode }]">
    <button @click="darkMode = !darkMode">
      {{ darkMode ? '☀️ Modo Claro' : '🌙 Modo Oscuro' }}
    </button>

    <h1>Usuarios en Vue 3</h1>

    <!-- ESPEJO DE DOBLE VÍA (v-model) -->
    <input v-model.trim="busqueda" placeholder="Buscar por nombre..." />

    <select v-model="ciudadFiltro">
      <option value="">Todas las ciudades</option>
      <option value="Gwenborough">Gwenborough</option>
      <option value="Wisokyburgh">Wisokyburgh</option>
      <option value="McKenziehaven">McKenziehaven</option>
    </select>

    <button @click="limpiarFiltros">Limpiar</button>

    <!-- ESTADOS DE CARGA Y ERROR (v-if / v-else-if / v-else) -->
    <p v-if="cargando">Cargando...</p>
    <p v-else-if="error">{{ error }}</p>

    <div v-else>
      <p>{{ usuariosFiltrados.length }} usuarios encontrados</p>
      <p>⭐ Favoritos guardados: {{ favoritos.length }}</p>

      <div v-if="favoritos.length > 0" class="caja-favoritos">
        <strong>Mis favoritos: </strong>
        {{ favoritos.map((fav) => fav.name).join(', ') }}
      </div>

      <!-- BUCLE (v-for) + PROPS (:) + EMITS (@) -->
      <UsuarioCard
        v-for="u in usuariosFiltrados"
        :key="u.id"
        :nombre="u.name"
        :email="u.email"
        :esFavorito="favoritos.some((fav) => fav.id === u.id)"
        @seleccionar="usuarioSeleccionado = u"
        @toggle-favorito="toggleFavorito(u)"
      />
    </div>

    <!-- DETALLE CONDICIONAL -->
    <div v-if="usuarioSeleccionado" class="caja-detalle">
      <h2>Detalle de {{ usuarioSeleccionado.name }}</h2>
      <p>Teléfono: {{ usuarioSeleccionado.phone }}</p>
      <p>Ciudad: {{ usuarioSeleccionado.address.city }}</p>
      <button @click="usuarioSeleccionado = null">Cerrar detalle</button>
    </div>
  </div>
</template>

<style scoped>
.contenedor {
  padding: 20px;
  font-family: sans-serif;
  min-height: 100vh;
  background-color: #ffffff;
  color: #111111;
}
.oscuro {
  background-color: #1e1e1e;
  color: #f5f5f5;
}
.caja-favoritos {
  margin-bottom: 15px;
  padding: 10px;
  background: #fff8dc;
  color: #111111;
  border-radius: 8px;
}
.caja-detalle {
  margin-top: 20px;
  padding: 15px;
  background: #eaeaea;
  color: #111111;
  border-radius: 8px;
}
</style>