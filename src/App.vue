<script setup>
  import { ref, reactive, onMounted, watch} from "vue";
  import { db } from "./data/guitarras";
  import Guitarra from "./components/Guitar.vue";
  import Header from "./components/Header.vue";
  import Footer from "./components/Footer.vue";

  //Con reactive
  /* const state = reactive({
        guitarras: [],
    })
  console.log(state.guitarras) */

  // Con ref
  const guitarras = ref([]);//Colección de datos
  const carrito = ref([]);//Para el llenado de nuestro carrito
  const guitarraHeader = ref({});//guitarra principal con boton en el inicio

  watch(carrito, () => {
    guardarLocalStorage();
  },{
    deep:true
  });

  onMounted(() => {
    guitarras.value = db;
    guitarraHeader.value = db[3];//Se elige la guitarra en oferta.
    /* state.guitarras = db; */
    const carritoStorage = localStorage.getItem('carrito');
    if(carritoStorage){
      carrito.value = JSON.parse(carritoStorage);
    }
  });

  const guardarLocalStorage = () => {
    localStorage.setItem ('carrito', JSON.stringify(carrito.value));
  };

  const agregarCarrito = ( guitarra )=>{
    const existeCarrito = carrito.value.findIndex( producto => producto.id === guitarra.id );
    if ( existeCarrito >= 0 ){
      carrito.value[existeCarrito].cantidad++;
    }else{
      guitarra.cantidad = 1;
      carrito.value.push(guitarra);  
    }

    guardarLocalStorage();
  }
  
  const decrementarProducto = (id) => {
    const index = carrito.value.findIndex( producto => producto.id === id );
    if(carrito.value[index].cantidad  <= 1) return;  
    carrito.value[index].cantidad--;    
  }
  const incrementarProducto = (id) => {
    const index = carrito.value.findIndex( producto => producto.id === id );
    if(carrito.value[index].cantidad  >= 5) return;  
    carrito.value[index].cantidad++;    
  }
  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id); //Me trae a los que son diferentes al que presione
  };
  const vaciarCarrito = () => {
    carrito.value = [];
  };
  
</script>

<template>
  <!-- Custom events -->
  <Header 
    :carrito="carrito"
    :guitarraHeader="guitarraHeader"
    @decrementar-producto="decrementarProducto" 
    @incrementar-producto="incrementarProducto"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
  />
  
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>
    <div class="row mt-5">
      <Guitarra 
        v-for="guitarra in guitarras" v-bind:key="guitarra.id" 
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
    </div>
  </main>

  <Footer />
</template>


