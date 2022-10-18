<script setup lang="ts">
import { inject, ref, type Ref } from 'vue';

let updateAfterRegistryDoneInstance=inject('updateAfterRegistryDone') as Ref<number>;
let nombreCompletoRef=ref('');
let edadRef=ref(0);

function closeModalAfterRegistry(){
    updateAfterRegistryDoneInstance.value=0;
}

function registerNewUser(){
    let registerUser = new XMLHttpRequest();
    //console.log(nombreCompletoRef.value,edadRef.value);
    registerUser.open("POST", "http://localhost:8080/ws/usuario.wsdl", false);
    registerUser.setRequestHeader("Content-type", "text/xml");
    registerUser.setRequestHeader("SOAPAction", "http://localhost:8080/soap/save-user");

    registerUser.onreadystatechange=()=>{
        if(registerUser.readyState===XMLHttpRequest.DONE){
            if(registerUser.status===200){
                console.log(registerUser.responseXML);
                alert('USUARIO REGISTRADO EXITOSAMENTE');
                closeModalAfterRegistry();
            }else{
                alert(registerUser.statusText+' '+registerUser.status);
            }
        }
    }

    registerUser.send(`
    <Envelope xmlns="http://schemas.xmlsoap.org/soap/envelope/">
        <Body>
            <saveUsuariosRequest xmlns="http://webspring.io/gs-prod">
                <name>
                    <id>[long]</id>
                    <nombreCompleto>${nombreCompletoRef.value}</nombreCompleto>
                    <edad>${edadRef.value}</edad>
                </name>
            </saveUsuariosRequest>
        </Body>
    </Envelope>
    `);
}
</script>
<template>
<section class="d-flex justify-content-center align-items-center min-vh-100 modal-register">
    <section class="card w-50 h-auto">
        <section class="card-body">
            <aside class="d-flex justify-content-center w-100">
                <header class="ms-auto h3 text-center">Registrar nuevo usuario</header>
                <button @click="closeModalAfterRegistry()" class="ms-auto btn-close"></button>
            </aside>
            <div class="mb-3">
                <label class="form-label" for="nombreCompletoInput">Nombre completo</label>
                <input v-model="nombreCompletoRef" type="text" id="nombreCompletoInput" class="form-control" placeholder="Ingrese nombre completo">
            </div>
            <div class="mb-3">
                <div class="mb-3">
                    <label for="edadInput" class="form-label">Edad</label>
                    <input v-model="edadRef" type="number" min="0" max="200" id="edadInput" class="form-control">
                </div>
            </div>
            <div class="mb-3">
                <button @click="registerNewUser()" class="btn btn-primary w-100">
                    Registrar usuario
                </button>
            </div>
        </section>
    </section>
</section>
</template>
<style scoped>
.modal-register{
    width: 100%;
    height: 100%;
    background-color: #00000033;
    position: fixed;
    z-index: 999999999;
}
</style>