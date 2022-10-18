<script setup lang="ts">
import NavBarComponent from "./NavBarComponent.vue";
import LoginBoxComponent from "./LoginBoxComponent.vue";
import { onMounted, ref } from "vue";
/*let dialogEstado=ref(null)
//let dialogEstadoChildren=ref(null);
onMounted(()=>{
    //dialogEstadoChildren.value=dialogEstado;
    console.log(dialogEstado.value.dialogEstado,' este es el del padre del login');
    
});
defineExpose(()=>{
    dialogEstado
});*/
</script>
<template>
    <NavBarComponent/>
    <section class="d-flex justify-content-center align-items-center min-vh-100 fondo-negro">
        <LoginBoxComponent/>
    </section>
</template>