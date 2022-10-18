<script setup lang="ts">
import { inject, ref, type Ref } from 'vue';
let dialogEstado = ref(0);
let removeLoginComponentAndNavBarUpdate = inject('removeLoginComponentAndNavBarUpdate') as Ref<number>;
/*Snippet for XML request referenced from https://stackoverflow.com/questions/124269/simplest-soap-example */
function iniciarSesionSolicitud(): void {
    let usuarioTxt = ((document.getElementsByName("usuario")[0]) as HTMLInputElement).value.trim();
    let contrasenaTxt = ((document.getElementsByName("contra")[0]) as HTMLInputElement).value.trim();

    if (usuarioTxt !== '' && contrasenaTxt !== '') {
        let requestXml = new XMLHttpRequest();
        let xmlResponsebject = {}
        requestXml.open("POST", "http://localhost:8080/ws/usuario.wsdl", true);
        requestXml.setRequestHeader("Content-type", "text/xml");
        requestXml.setRequestHeader("SOAPAction", "http://localhost:8080/soap/iniciar-sesion");

        requestXml.onreadystatechange = () => {
            if (requestXml.readyState === XMLHttpRequest.DONE) {
                if (requestXml.status === 200) {
                    //console.log(requestXml.responseText);
                } else {
                    //console.log(requestXml.status, requestXml.statusText);
                }
            }
        }

        /* We have to send the envelope, you can create it by your own but
        remember that spring already gives us all the envelopes
        with the servlet instances by every action specified in the schema*/
        requestXml.send(`
        <Envelope xmlns='http://schemas.xmlsoap.org/soap/envelope/'>
            <Body>
                <iniciarSesionRequest xmlns='http://webspring.io/gs-prod'>
                    <usuario>${usuarioTxt}</usuario>
                    <contrasena>${contrasenaTxt}</contrasena>
                </iniciarSesionRequest>
            </Body>
        </Envelope>`);
        removeLoginComponentAndNavBarUpdate.value = 1;
    } else {
        dialogEstado.value += 1;
        let fondoNegro = <HTMLElement>document.getElementsByClassName("fondo-negro")[0];
        fondoNegro.style.backgroundColor = "#00000077"
    }
}

function cerrarModal() {
    dialogEstado.value = 0;
    let fondoNegro = <HTMLElement>document.getElementsByClassName("fondo-negro")[0];
    fondoNegro.style.backgroundColor = "#ffffff"
}
</script>
<template>
    <dialog v-if="dialogEstado!==0" :key="dialogEstado" class="dialogBox" open>
        <button @click="cerrarModal" type="button" class="btn-close" aria-label="Close"></button>
        <p>Revise sus credenciales e intentelo de nuevo</p>
    </dialog>
    <section :key="dialogEstado" v-if="dialogEstado===0" class="card w-50 h-25">
        <section class="card-body">
            <div class="mb-3">
                <label for="usuario">Usuario</label>
                <input placeholder="Introduce tu usuario" type="text" name="usuario" class="form-control">
            </div>
            <div class="mb-3">
                <label for="">contrasena</label>
                <input placeholder="Introduce tu contraseña" type="password" name="contra" class="form-control">
            </div>
            <div class="mb-3 w-100 text-center">
                <button @click="iniciarSesionSolicitud()" class="btn btn-primary w-75">
                    Iniciar sesión
                </button>
            </div>
        </section>
    </section>
</template>
<style scoped>
.dialogBox {
    border-radius: 5px;
    border: 1px solid black;
}
</style>