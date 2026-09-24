<script setup>
import { onMounted, onUnmounted, ref } from 'vue'

const details = [
  { label: 'Día', value: 'Sábado 17 de octubre' },
  { label: 'Hora', value: '2:00 p. m.' },
  { label: 'Plan', value: 'Almuerzo campestre y tarde de tertulia' },
]

const images = Array.from({ length: 13 }, (_, index) => `/img/${index + 1}.jpeg`)
const currentImage = ref(0)
let carouselTimer

const goToImage = (index) => {
  currentImage.value = index
}

const showNextImage = () => {
  currentImage.value = (currentImage.value + 1) % images.length
}

const showPreviousImage = () => {
  currentImage.value = (currentImage.value - 1 + images.length) % images.length
}

onMounted(() => {
  carouselTimer = window.setInterval(showNextImage, 4500)
})

onUnmounted(() => {
  window.clearInterval(carouselTimer)
})
</script>

<template>
  <main class="invitation">
    <div class="leaf leaf-left" aria-hidden="true"></div>
    <div class="leaf leaf-right" aria-hidden="true"></div>

    <section class="invitation-card" aria-labelledby="invitation-title">
      <header class="invitation-intro">
        <div class="ornament" aria-hidden="true"><span></span><b>60</b><span></span></div>
        <p class="eyebrow">Una tarde para celebrar</p>
        <h1 id="invitation-title">Familia y amigos</h1>
      </header>

      <section class="message section-block" aria-labelledby="message-title">
        <p class="section-kicker" id="message-title">Una invitación especial</p>
        <p>Eres parte importante de mi historia de vida. Con gratitud y cariño quiero compartir contigo un momento íntimo y acogedor.</p>
        <p>Por eso, quiero que me acompañes a celebrar mis 60 años de vida.</p>
        <p>Te espero para disfrutar de un almuerzo campestre y de una tarde de tertulia.</p>
      </section>

      <section class="details-block section-block" aria-labelledby="details-title">
        <p class="section-kicker" id="details-title">Guarda esta fecha</p>
        <div class="event-details" aria-label="Detalles de la celebración">
          <article v-for="detail in details" :key="detail.label" class="detail">
            <span class="detail-label">{{ detail.label }}</span>
            <strong>{{ detail.value }}</strong>
          </article>
        </div>
      </section>

      <section class="location section-block" aria-labelledby="location-title">
        <span class="detail-label">Ubicación</span>
        <strong id="location-title">Finca La Provincia</strong>
        <p>Un kilómetro después del Sena La Salada, Caldas, Antioquia</p>
        <a
          href="https://maps.app.goo.gl/3jvc6Ea9aFAGiu91A?g_st=iwb"
          target="_blank"
          rel="noopener noreferrer"
        >
          Ver ubicación en Google Maps
        </a>
      </section>

      <section class="memory-section section-block" aria-labelledby="memory-title">
        <div class="section-heading">
          <p class="section-kicker">Un pedacito de mi historia</p>
          <h2 id="memory-title">Así he vivido estos años</h2>
          <p>Quiero compartir contigo algunas fotografías de mi vida, recuerdos que atesoro y que hacen aún más especial esta celebración.</p>
        </div>

        <div class="carousel" aria-label="Galería de recuerdos">
        <div class="carousel-frame">
          <img
            v-for="(image, index) in images"
            :key="image"
            :src="image"
            :alt="`Recuerdo ${index + 1} de 13`"
            class="carousel-image"
            :class="{ 'is-active': currentImage === index }"
            :aria-hidden="currentImage !== index"
          />
          <button class="carousel-control carousel-control-previous" type="button" aria-label="Imagen anterior" @click="showPreviousImage">
            <span aria-hidden="true">‹</span>
          </button>
          <button class="carousel-control carousel-control-next" type="button" aria-label="Imagen siguiente" @click="showNextImage">
            <span aria-hidden="true">›</span>
          </button>
        </div>
        <div class="carousel-dots" aria-label="Seleccionar imagen">
          <button
            v-for="(image, index) in images"
            :key="`${image}-dot`"
            class="carousel-dot"
            :class="{ 'is-active': currentImage === index }"
            type="button"
            :aria-label="`Mostrar recuerdo ${index + 1}`"
            :aria-current="currentImage === index ? 'true' : undefined"
            @click="goToImage(index)"
          ></button>
        </div>
        </div>
      </section>

      <section class="confirmation-block section-block" aria-labelledby="confirmation-title">
        <p class="section-kicker">Tu compañía es el mejor regalo</p>
        <div class="confirmation">
          <span class="confirmation-icon" aria-hidden="true">✓</span>
          <div class="confirmation-copy">
            <strong id="confirmation-title">Favor confirmar asistencia</strong>
            <p>Antes del 10 de octubre</p>
          </div>
        </div>
      </section>
    </section>
  </main>
</template>
