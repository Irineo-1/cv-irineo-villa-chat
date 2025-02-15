<script setup>

import { reactive, ref, nextTick } from "vue"
import "../styles/phone.css"
import cv from "../info/cv.json"

const { name, summary, email, github, linkedin } = cv.basics
const { city, region } = cv.basics.location

const mensajes = reactive([])
let mensaje = ref("")
const chatContainer = ref(null)
const divOptions = ref(null)
let iconArrow = ref("/arrow_up.svg")
const mensajesPredeterminados = ['acerca de mi', 'información de contacto', 'experiencia', 'educación', 'certificados', 'habilidades', 'proyectos']

const scrollToBottom = () => {
  nextTick(() => {

    if (chatContainer.value == null) return
    chatContainer.value.scrollTop = chatContainer.value.scrollHeight

  })
}

const sendMessage = () => {

    if(mensaje.value.trim() === '') return

    if(mensajesPredeterminados.includes(mensaje.value.toLowerCase().trim())) {

        mensajes.push({
            "is": "user",
            "mensaje": `${mensaje.value}`
        })

        switch(mensaje.value.toLowerCase()) {
            case "acerca de mi":
                mensajes.push({
                    "is": "page",
                    "mensaje": `Soy un ${summary.toLowerCase()}`
                })
            break
            case "información de contacto":
                mensajes.push({
                    "is": "page",
                    "mensaje": `
                        Mi información de contacto es correo: ${email}
                        <a href="mailto:${email}" class='btn-contacto'><img src="/email.svg" alt="Location"></a>
                        Github: <a href='${github}' target="_blank" class='btn-contacto'><img src='/github.svg' alt="github"/></a>
                        Linkedin: <a href='${linkedin}' target="_blank" class='btn-contacto'><img src='/linkedin.svg' alt="linkedin"/></a>
                    `
                })
            break

            case "experiencia":
                cv.work.forEach(el => {
                    mensajes.push({
                        "is": "page",
                        "mensaje": `
                            <strong>Empresa:</strong> ${el.name}, <strong>posicion:</strong> ${el.position},
                            <strong>Inicio:</strong> ${el.startDate} <strong>Fin:</strong> ${el.endDate},
                            <strong>Actividades:</strong> ${el.summary}
                        `
                    })
                })
            break

            case "educación":
                cv.education.forEach(el => {
                    mensajes.push({
                        "is": "page",
                        "mensaje": `
                            <strong>Institucion:</strong> <a href='${el.url}' target="_blank" class='location'>${el.institution}</a>.
                            <strong>Area:</strong> ${el.area}.
                            <strong>Typo:</strong> ${el.studyType}.
                            <strong>Inicio:</strong> ${el.startDate} <strong>Fin:</strong> ${el.endDate}.
                        `
                    })
                })
            break

            case "certificados":
                cv.certificates.forEach(el => {
                    mensajes.push({
                        "is": "page",
                        "mensaje": `
                            <a href='${el.url}' target="_blank" class='location'>${el.name} ${el.date} ${el.issuer}</a>.
                        `
                    })
                })
            break

            case "habilidades":
                    mensajes.push({
                        "is": "page",
                        "mensaje": `
                            Mis Habilidades son: 
                        `
                    })
                cv.skills.forEach(el => {
                    mensajes.push({
                        "is": "page",
                        "mensaje": `
                            ${el.name}
                        `
                    })
                })
            break

            case "proyectos":
                cv.projects.forEach(el => {
                    mensajes.push({
                        "is": "page",
                        "mensaje": `
                            <strong>Proyecto:</strong> <a href='${el.url}' class='location' target="_blank">${el.name}</a>.
                            <strong>Descripcion:</strong> ${el.description}.
                            <strong>Tecnologias:</strong> <span class='tecnologias-text'>${el.tecnologis.join(', ')}</span>
                        `
                    })
                })
            break
        }

        scrollToBottom()
    }

    mensaje.value = ""
}

const toggleOptions = () => {
    if (!divOptions.value) return
    
    if (divOptions.value.style.height === '160px') {
        divOptions.value.style.height = '0px'
        iconArrow.value = "/arrow_up.svg"
    } else {
        divOptions.value.style.height = '160px'
        iconArrow.value = "/arrow_donw.svg"
    }
}

const sendMessageClicked = (message) => {
    mensaje.value = message
    toggleOptions()
    sendMessage()
}

mensajes.push({
    "is": "page",
    "mensaje": `Hola! me llamo ${name}`
})

mensajes.push({
    "is": "page",
    "mensaje": `soy de <a href='https://www.google.com/maps/place/Colima' class='location' target="_blank">${city}, ${region}</a>`
})

mensajes.push({
    "is": "page",
    "mensaje": `
        Mi información de contacto es correo: ${email}
        <a href="mailto:${email}" class='btn-contacto'><img src="/email.svg" alt="Location"></a>
        Github: <a href='${github}' target="_blank" class='btn-contacto'><img src='/github.svg' alt="github"/></a>
        Linkedin: <a href='${linkedin}' target="_blank" class='btn-contacto'><img src='/linkedin.svg' alt="linkedin"/></a>
    `
})

mensajes.push({
    "is": "page",
    "mensaje": `
        Puedes enviar los siguentes mensajes: 
        <strong>acerca de mi, información de contacto, experiencia, educación, certificados, habilidades, proyectos</strong>
    `
})

</script>

<template>

    <div class="container-phone">

        <div class="phone">
            <div class="screen">
                <div class="content-camara">
                    <div class="camara"><div class="foco"></div></div>
                </div>

                <div class="topbar">
                    <div>
                        <img src="/arrow_left.svg" class="btn-adorno" alt="atras">
                        <span style="font-size: 1rem; font-weight: 700;">{{name.split(' ')[1]}}</span>
                    </div>
                    <div>

                        <img src="/videocam.svg" class="btn-adorno" alt="video">

                        <img src="/phone.svg" class="btn-adorno" alt="phone">

                        <img src="/more.svg" class="btn-adorno" alt="more">

                    </div>
                </div>

                <div class="chat" ref="chatContainer">

                    <div v-for="mensaje, id in mensajes" :key="id">

                        <div v-if="mensaje.is == 'page'" class="container-message-page">
                            <div class="message-page">
                                <span v-html="mensaje.mensaje" class="message-text"></span>
                            </div>
                        </div>

                        <div v-else class="container-message-user">
                            <div class="message-user">
                                <span class="message-text">{{mensaje.mensaje}}</span>
                            </div>
                        </div>

                    </div>
                </div>

                <button class="btn-arrow" @click="toggleOptions">
                    <img :src="iconArrow" alt="arrow">
                </button>

                <div class="options" ref="divOptions">
                    <div class="container-options">

                        <div v-for="opcion in mensajesPredeterminados">
                            <button class="btn-options-messages" @click="sendMessageClicked(opcion)">{{opcion}}</button>
                        </div>

                    </div>
                </div>

                <div class="controles">
                    <input type="text" placeholder="Escribe tu mensaje!" class="message-box" v-model="mensaje" @keyup.enter="sendMessage">
                    <button class="btn-send" @click="sendMessage">
                        <img src="/ic_send.svg" alt="" srcset="">
                    </button>
                </div>
            </div>
        </div>
    
    </div>

</template>