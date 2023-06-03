<script setup>
    import { truncarDescripcion } from '../helpers/funciones.js';

    const props = defineProps({
        guitarra: {
            type: Object,
            required: true
        }
    })

    defineEmits(['agregar-carrito'])
   
</script>

<template>
    <div class="col-md-6 col-lg-4 my-4 row align-items-center">
        <div class="col-4">
            <img class="img-fluid" :src="guitarra.imagen.data.attributes.url" alt="imagen guitarra">
        </div>
        <div class="col-8">
            <h3 class="text-black fs-4 fw-bold text-uppercase">{{ guitarra.nombre }}</h3>
            <p>{{ truncarDescripcion(guitarra.descripcion, 122) }}</p>
            <p v-if="guitarra.stock == 0 || guitarra.stock == null" class="text-end out-stock">Fuera de stock</p>
            <p v-else-if="guitarra.stock == 1" class="text-end">
                <span class="fw-bold stock-quantity">
                    ¡Ultima disponible!
                </span>
            </p>
            <p v-else class="text-end">
                <span class="text-black fw-bold">
                    <span class="stock-quantity">{{ guitarra.stock }}</span> disponibles
                </span>
            </p>
            <p class="fw-black text-primary fs-3">{{ guitarra.precio.toLocaleString('es-MX', { style: 'currency', currency: 'MXN' }) }}</p>
            <button 
                type="button" 
                class="btn btn-dark w-100 "
                :disabled="guitarra.stock === 0 || guitarra.stock === null"
                @click="$emit('agregar-carrito', guitarra)"
                >Agregar al Carrito</button>
        </div>
    </div><!-- FIN GUITARRA -->
</template>


<style lang="css" scoped>
.out-stock {
    color: red;
}
.stock-quantity {
    color: #ff9900;
}
</style>