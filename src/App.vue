<script setup>
import { ref, onMounted, watch } from 'vue'
import Header from './components/Header.vue'
import Guitarra from './components/Guitarra.vue'
import Footer from './components/Footer.vue'

const guitarras = ref([])
const carrito = ref([])
const guitarraDestacada = ref({})

watch(carrito, () => {
    guardarLocalStorage();
}, {
    deep: true
})


onMounted(() => {
    const apiUrl = `${import.meta.env.VITE_API_URL}/api/guitarras?populate=imagen`

    fetch(apiUrl)
        .then(resp => resp.json())
        .then(data => {
            guitarras.value = data.data.map((item, index) => {
                const guitarra = item.attributes;
                guitarra.id = index.toString(); 
                if (guitarra.url === 'vai') {
                    guitarraDestacada.value = guitarra;
                }

                return guitarra;
            });
        })
        .catch(err => console.log(err))

    // Obtener el carrito del localStorage
    const carritoStorage = JSON.parse(localStorage.getItem('carrito'));
    if (carritoStorage) {
        carrito.value = carritoStorage;
    }
})

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value));
}

const agregarCarrito = (guitarra) => {
    comprobarStock(guitarra);
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)

    if (existeCarrito >= 0) {
        if (carrito.value[existeCarrito].cantidad < carrito.value[existeCarrito].stock) {
            carrito.value[existeCarrito].cantidad++;
        } else {
            carrito.value[existeCarrito].sinStock = true;
            return;
        }

    } else {
        if (guitarra.stock > 0) {
            guitarra.cantidad = 1;
            carrito.value.push(guitarra);
        } else {
            guitarra.sinStock = true;
            return;
        }
    }

    guardarLocalStorage();
}

const comprobarStock = (guitarra) => {
    const { stock } = guitarra;

    if (stock === 0) {
        guitarra.sinStock = true;
    } else {
        guitarra.sinStock = false;
    }
}

const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if (carrito.value[index].cantidad <= 1) return;
    carrito.value[index].cantidad--;
}

const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if (carrito.value[index].cantidad >= carrito.value[index].stock) return;
    carrito.value[index].cantidad++;
}

const eliminarProducto = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    carrito.value.splice(index, 1);
}

const vaciarCarrito = () => {
    carrito.value = [];
}

</script>

<template>
    <Header :carrito="carrito" :guitarraDestacada="guitarraDestacada" @decrementar-cantidad="decrementarCantidad"
        @incrementar-cantidad="incrementarCantidad" @agregar-carrito="agregarCarrito" @eliminar-producto="eliminarProducto"
        @vaciar-carrito="vaciarCarrito" />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra 
                v-for="guitarra in guitarras" :guitarra="guitarra" :key="guitarra.url" @agregar-carrito="agregarCarrito" 
            />
        </div>
    </main>

    <Footer />
</template>