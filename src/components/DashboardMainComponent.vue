<script setup lang="ts">
import { getCurrentInstance, provide, ref, type ComponentOptionsBase, type ComponentPublicInstance, type Ref } from "vue";
import NavBarComponent from "./NavBarComponent.vue";
import ModalCreateNewUser from "./ModalCreateNewUser.vue";

interface usuarioAtributtes{
    id:string,
    nombreCompleto?:string,
    edad?:string
}

let updateAfterRegistryDone=ref(0) as Ref<number>;
let xmlResponsebject:Array<usuarioAtributtes> = [];

function obtenerFetchInicial(){
    let bringAllUsuarios = new XMLHttpRequest();
    bringAllUsuarios.open("POST", "http://localhost:8080/ws/usuario.wsdl", false);
    bringAllUsuarios.setRequestHeader("Content-type", "text/xml");
    bringAllUsuarios.setRequestHeader("SOAPAction", "http://localhost:8080/soap/get-all-usuarios");

    bringAllUsuarios.onreadystatechange = () => {
        if (bringAllUsuarios.readyState === XMLHttpRequest.DONE) {
            if (bringAllUsuarios.status === 200) {
                /*Snippet taken from https://stackoverflow.com/questions/27438590/extract-value-from-xml-using-pure-javascript */        
                var XMLListIterator = ((((bringAllUsuarios.responseText) as string).match(/<ns2:usuariosLista>(.*?)<\/ns2:usuariosLista>/g)) as RegExpMatchArray);
                if(XMLListIterator!==undefined || XMLListIterator!==null){
                    XMLListIterator.forEach((value, id) => {
                        let parsedXMLListElement=XMLListIterator[id].replace(/(<([^>]+)>)/ig, ",").replace(",,","").split(",,");
                        xmlResponsebject.push({
                            'id':parsedXMLListElement[0],
                            'nombreCompleto':parsedXMLListElement[1],
                            'edad':parsedXMLListElement[2]
                        });
                    })
                    console.log(xmlResponsebject);
                }else{
                    alert('No hay usuarios en el sistema')
                }
            } else {
                console.log(bringAllUsuarios.status, bringAllUsuarios.statusText);
            }
        }
    }

    /* We have to send the envelope, you can create it by your own but
    remember that spring already gives us all the envelopes
    with the servlet instances by every action specified in the schema*/
    bringAllUsuarios.send(
        `<Envelope xmlns="http://schemas.xmlsoap.org/soap/envelope/">
            <Body>
                <getUsuariosRequest xmlns="http://webspring.io/gs-prod">
                    <name>[string]</name>
                </getUsuariosRequest>
            </Body>
        </Envelope>`
    );
}

function deleteUsuario(id:number,prueba:any):void{ 
    let deleteXmlRequest=new XMLHttpRequest();
    deleteXmlRequest.open('POST','http://localhost:8080/ws/usuario.wdsl',true);
    deleteXmlRequest.setRequestHeader('Content-type','text/xml');
    deleteXmlRequest.setRequestHeader('SOAPAction','http://localhost:8080/soap/delete-user');

    deleteXmlRequest.onreadystatechange=()=>{
        if(deleteXmlRequest.readyState===XMLHttpRequest.DONE){
            if(deleteXmlRequest.status===200){
                console.log(deleteXmlRequest.responseXML);
                prueba.target.parentElement.parentElement.replaceChildren();
                alert('USUARIO ELIMINADO');                
                xmlResponsebject=[];
                obtenerFetchInicial();
            }else{
                alert(deleteXmlRequest.statusText+' '+deleteXmlRequest.status);
            }
        }
    }

    deleteXmlRequest.send(`
    <Envelope xmlns="http://schemas.xmlsoap.org/soap/envelope/">
        <Body>
            <deleteByIdRequest xmlns="http://webspring.io/gs-prod">
                <id>${id}</id>
            </deleteByIdRequest>
        </Body>
    </Envelope>`);
}

function openRegistryModal(){
    updateAfterRegistryDone.value=1;
}

obtenerFetchInicial();
provide('updateAfterRegistryDone',updateAfterRegistryDone);
</script>

<template>
    <NavBarComponent />
    <ModalCreateNewUser v-if="updateAfterRegistryDone===1" :key="updateAfterRegistryDone"/>
    <section class="d-flex justify-content-around">
        <header class="h3 text-center">SOAP Based crud with spring boot and vue</header>
        <button @click="openRegistryModal()" class="btn btn-primary">Crear nuevo usuario</button>
    </section>
    <section class="d-flex justify-content-center align-items-start min-vh-100 fondo-negro">
        <table class="table">
            <thead>
                <tr>
                    <th scope="col">ID</th>
                    <th scope="col">Nombre completo</th>
                    <th scope="col">Edad</th>
                    <th scope="col">Opciones</th>
                </tr>
            </thead>
            <tbody id="clear-table-ins">
                <tr v-if="xmlResponsebject.length>0" v-for="(item,index) in xmlResponsebject" :key="item.id">
                    <th scope="row">{{item.id}}</th>
                    <td>{{item.nombreCompleto}}</td>
                    <td>{{item.edad}}</td>
                    <td><button class="btn btn-info">Modificar</button>
                        <button @click="deleteUsuario(+item.id,$event)"  class="btn btn-danger">Borrar</button></td>
                </tr>
            </tbody>
        </table>
    </section>


</template>
<style scoped></style>