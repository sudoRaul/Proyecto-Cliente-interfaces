<script setup>
import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";
import Swal from "sweetalert2";
import NoData from "@/components/NoData.vue";


const route = useRoute();
const localidad = ref(route.params.localidad || "");
const fecha = ref(route.params.fecha);
const listaRutas = ref([]);



async function obtenerRutasFiltradas() {
    try {
        // En caso de tener localidad se la añadimos a la URL
        let url = `http://localhost/APIFreetours/api.php/rutas?fecha=${fecha.value}`;
        if (localidad.value) {
            url += `&localidad=${localidad.value}`;
        }

        const response = await fetch(url);
        if (!response.ok) throw new Error("Error al obtener las rutas");

        const data = await response.json();
        listaRutas.value = data;
    } catch (error) {
        Swal.fire({
            icon: "error",
            title: "Error",
            text: error.message
        });
    }
}

// Llamamos a la función cuando el componente se monte
onMounted(obtenerRutasFiltradas);
</script>

<template>
    <div v-if="listaRutas.length > 0" class="container">
        <h1 class="text-center my-4">Rutas {{ localidad ? `en ${localidad}` : "" }} para la fecha {{ fecha }}</h1>


        <div class="tarjetas d-flex flex-column align-items-center w-100">
            <router-link 
                v-for="ruta in listaRutas" 
                :key="ruta.id" 
                :to="`/info-completa-ruta/${ruta.id}`"
                class="tarjeta bg-white shadow rounded row pt-4 pb-5 mb-4 col-md-10 col-lg-8 text-decoration-none text-dark">
                <h2 class="text-center">{{ ruta.titulo }}</h2>
                <img :src="ruta.foto" title="Imagen de la ruta" alt="Imagen de la ruta" 
                    class="rounded col-5">
                
                <div class="col-7 d-flex flex-column justify-content-center">
                    <p class="text-gray-700 font-semibold col-12 fs-5">📅 {{ ruta.fecha }}</p>
                    <p class="text-gray-500 col-12 fs-5">📍 {{ ruta.localidad }}</p>
                    <p class="text-gray-500 col-12 fs-5">👉 Pulse aquí para ver más información de la ruta</p>
                </div>
            </router-link>
        </div>
    </div>
    <NoData v-else mensaje="No se encontraron rutas" submensaje="Pruebe en otra fecha o vuelva en otro momento." />

</template>

<style scoped>
.tarjeta {
    min-height: 250px; 
    padding: 20px;
    transition: transform 0.2s ease-in-out;
}

.tarjeta:hover {
    transform: scale(1.02);
}
</style>



