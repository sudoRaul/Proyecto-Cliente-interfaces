<script setup>
import { ref, onMounted } from "vue";

const listaRutas = ref([]);
const error = ref(null);
const idGuia = JSON.parse(localStorage.getItem("sesion")).id;

async function obtenerRutas() {
    try {
        const response = await fetch(`http://localhost/APIFreetours/api.php/asignaciones?guia_id=${idGuia}`);
        if (!response.ok) throw new Error("Error al obtener las rutas");

        const data = await response.json();
                
        listaRutas.value = data;
    } catch (err) {
        error.value = err.message;
    }
}

/*onMounted(() => {
    obtenerRutas();
});*/
obtenerRutas();
</script>

<template>
    <div>
        <h2 class="text-center mt-5 mb-5">Listado de rutas pendientes</h2>
        <p v-if="error" class="text-red-500">{{ error }}</p>

        <div class="tarjetas justify-content-between">
            <div v-for="ruta in listaRutas" :key="ruta.ruta_id" class="bg-white shadow rounded row pt-4 pb-5">
                <img :src="ruta.ruta_foto" alt="Imagen de la ruta" class="rounded col-5">
                <div class="col-7">
                    <p class="text-gray-700 font-semibold">📅 {{ ruta.ruta_fecha }}</p>

                    <p class="font-semibold mt-3">🧑‍🤝‍🧑 Asistentes:</p>
                    <ul>
                        <li v-for="reserva in ruta.reservas" :key="reserva.reserva_id">
                            - {{ reserva.cliente.nombre }}
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>
<style scoped>
.tarjetas {
    width: 50%;
    margin: auto;
}
</style>
