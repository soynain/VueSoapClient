<script setup lang="ts">
import {ref} from 'vue';
const estadoCerrarSesion=ref(0);
console.log(estadoCerrarSesion.value);
</script>
<template>
    <header>
    <nav class="navbar navbar-expand-lg bg-light">
      <div class="container-fluid">
        <a class="navbar-brand mb-0 h1" href="#">SOAP CLIENTE</a>
      </div>
      <ul v-if="estadoCerrarSesion!==0" class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item"><a class="nav-link">Cerrar sesión</a></li>
      </ul>
    </nav>
  </header>
</template>