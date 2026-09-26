<script setup>
import { onMounted, onUnmounted, ref } from 'vue'

const details = [
  { label: 'Día', value: 'Sábado 17 de octubre' },
  { label: 'Hora', value: '2:00 p. m.' },
  { label: 'Plan', value: 'Almuerzo campestre y tarde de tertulia' },
]

const images = Array.from({ length: 13 }, (_, index) => `/img/${index + 1}.jpeg`)
const currentImage = ref(0)
const showConfirmationModal = ref(false)
const showLightbox = ref(false)
const lightboxImage = ref(0)
const isSubmitting = ref(false)
const submitStatus = ref(null)
let carouselTimer

// Touch handling for swipe
let touchStartX = 0
let touchEndX = 0

const goToImage = (index) => {
  currentImage.value = index
}

const showNextImage = () => {
  currentImage.value = (currentImage.value + 1) % images.length
}

const showPreviousImage = () => {
  currentImage.value = (currentImage.value - 1 + images.length) % images.length
}

const openConfirmationModal = () => {
  showConfirmationModal.value = true
  submitStatus.value = null
}

const closeConfirmationModal = () => {
  showConfirmationModal.value = false
  submitStatus.value = null
}

const openLightbox = (index) => {
  lightboxImage.value = index
  showLightbox.value = true
}

const closeLightbox = () => {
  showLightbox.value = false
}

const showNextLightboxImage = () => {
  lightboxImage.value = (lightboxImage.value + 1) % images.length
}

const showPreviousLightboxImage = () => {
  lightboxImage.value = (lightboxImage.value - 1 + images.length) % images.length
}

// Touch event handlers for swipe
const handleTouchStart = (e) => {
  touchStartX = e.changedTouches[0].screenX
}

const handleTouchMove = (e) => {
  touchEndX = e.changedTouches[0].screenX
}

const handleTouchEnd = () => {
  const swipeThreshold = 50
  const diff = touchStartX - touchEndX
  
  if (Math.abs(diff) > swipeThreshold) {
    if (diff > 0) {
      // Swipe left - next image
      showNextLightboxImage()
    } else {
      // Swipe right - previous image
      showPreviousLightboxImage()
    }
  }
}

const submitConfirmation = async (event) => {
  event.preventDefault()
  isSubmitting.value = true
  submitStatus.value = null

  const formData = new FormData(event.target)
  
  // Formatear fecha en zona horaria Colombia (UTC-5)
  const now = new Date()
  const colombiaOffset = -5 * 60 // Colombia es UTC-5 (en minutos)
  const localOffset = now.getTimezoneOffset()
  const colombiaTime = new Date(now.getTime() + (colombiaOffset - localOffset) * 60000)
  
  const data = {
    name: formData.get('name'),
    guests: formData.get('guests'),
    message: formData.get('message') || '',
    timestamp: colombiaTime.toLocaleString('es-CO', {
      timeZone: 'America/Bogota',
      year: 'numeric',
      month: '2-digit',
      day: '2-digit',
      hour: '2-digit',
      minute: '2-digit',
      second: '2-digit',
      hour12: true
    })
  }

  try {
    // Aquí iría la URL de tu Google Apps Script
    const GOOGLE_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbzwmGiBPIr32_3y_68KnXFn6Ec65uh2pFMRs3p67hHpqOmtX2iFSvtCkZO8r-D_0-KV/exec'
    
    const response = await fetch(GOOGLE_SCRIPT_URL, {
      method: 'POST',
      mode: 'no-cors',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data)
    })

    // Como usamos no-cors, no podemos leer la respuesta, pero asumimos éxito
    submitStatus.value = 'success'
    
    // Limpiar el formulario
    event.target.reset()
    
    // Cerrar el modal después de 2 segundos
    setTimeout(() => {
      closeConfirmationModal()
    }, 2000)

  } catch (error) {
    console.error('Error al enviar:', error)
    submitStatus.value = 'error'
  } finally {
    isSubmitting.value = false
  }
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
        <p class="eyebrow">Una tarde especial</p>
        <h1 id="invitation-title">Familia y amigos</h1>
      </header>

      <section class="message section-block" aria-labelledby="message-title">
        <p class="section-kicker" id="message-title">Una invitación especial</p>
        <p>Eres parte importante de mi historia de vida. Con gratitud y cariño quiero compartir contigo un momento lleno de alegría y buena compañía.</p>
        <p>Por eso, quiero que me acompañes en este paso tan especial de mi vida.</p>
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
          <p>Quiero compartir contigo algunas fotografías de mi vida, recuerdos que atesoro y que hacen aún más especial este momento.</p>
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
            @click="openLightbox(index)"
            style="cursor: pointer;"
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

      <section class="dress-code section-block" aria-labelledby="dress-code-title">
        <div class="dress-code-card">
          <div class="dress-code-icon" aria-hidden="true">
            <svg viewBox="0 0 120 120" role="img" aria-label="Icono de vestido">
              <path d="M40 24L60 12L80 24L72 42L92 92H82L74 60H46L38 92H28L48 42L40 24ZM54 42H66V92H54V42ZM32 92H88L84 102H36L32 92Z" fill="none" stroke="currentColor" stroke-width="4" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
          </div>
          <h2 id="dress-code-title">Código de Vestuario</h2>
          <p class="dress-line">No te estreses! Ven tranquilo/a.</p>
          <p class="dress-line">Ropa casual, eso sí, que quedes memorable en las fotos.</p>
          <p class="dress-line">Lo importante para mí es tu compañía.</p>
        </div>
      </section>

      <section class="confirmation-block section-block" aria-labelledby="confirmation-title">
        <p class="section-kicker">Tu compañía es el mejor regalo</p>
        <div class="confirmation">
          <span class="confirmation-icon" aria-hidden="true">✓</span>
          <div class="confirmation-copy">
            <strong id="confirmation-title">Favor confirmar asistencia</strong>
            <p>Antes del 6 de octubre</p>
          </div>
        </div>
        <button 
          class="confirmation-button" 
          type="button"
          @click="openConfirmationModal"
        >
          Confirma tu asistencia aquí
        </button>
      </section>
    </section>

    <!-- Lightbox Modal -->
    <div 
      v-if="showLightbox" 
      class="lightbox-overlay"
      @click="closeLightbox"
      role="dialog"
      aria-modal="true"
      aria-label="Visor de imágenes"
    >
      <button 
        class="lightbox-close" 
        type="button"
        @click="closeLightbox"
        aria-label="Cerrar visor"
      >
        ✕
      </button>
      
      <button 
        class="lightbox-control lightbox-control-previous" 
        type="button" 
        aria-label="Imagen anterior"
        @click.stop="showPreviousLightboxImage"
      >
        <span aria-hidden="true">‹</span>
      </button>
      
      <div 
        class="lightbox-content" 
        @click.stop
        @touchstart="handleTouchStart"
        @touchmove="handleTouchMove"
        @touchend="handleTouchEnd"
      >
        <img 
          :src="images[lightboxImage]"
          :alt="`Recuerdo ${lightboxImage + 1} de 13`"
          class="lightbox-image"
        />
        <div class="lightbox-counter">
          {{ lightboxImage + 1 }} / {{ images.length }}
        </div>
      </div>
      
      <button 
        class="lightbox-control lightbox-control-next" 
        type="button" 
        aria-label="Imagen siguiente"
        @click.stop="showNextLightboxImage"
      >
        <span aria-hidden="true">›</span>
      </button>
    </div>

    <!-- Modal de Confirmación -->
    <div 
      v-if="showConfirmationModal" 
      class="modal-overlay"
      @click="closeConfirmationModal"
      aria-labelledby="modal-title"
      role="dialog"
      aria-modal="true"
    >
      <div class="modal-content" @click.stop>
        <button 
          class="modal-close" 
          type="button"
          @click="closeConfirmationModal"
          aria-label="Cerrar formulario"
        >
          ✕
        </button>
        
        <div class="modal-header">
          <h2 id="modal-title">Confirma tu asistencia</h2>
          <p>Por favor completa el formulario para confirmar tu presencia</p>
        </div>

        <form class="confirmation-form" @submit="submitConfirmation">
          <div class="form-group">
            <label for="name">Nombre completo *</label>
            <input 
              id="name"
              type="text" 
              name="name"
              required
              placeholder="Tu nombre"
            />
          </div>

          <div class="form-group">
            <label for="guests">Número de personas *</label>
            <select 
              id="guests"
              name="guests"
              required
            >
              <option value="">Selecciona...</option>
              <option value="1">1 persona</option>
              <option value="2">2 personas</option>
              <option value="3">3 personas</option>
              <option value="4">4 personas</option>
              <option value="5">5 personas</option>
              <option value="6+">6 o más personas</option>
            </select>
          </div>

          <div class="form-group">
            <label for="message">Mensaje o pregunta (opcional)</label>
            <textarea 
              id="message"
              name="message"
              rows="3"
              placeholder="Algún mensaje especial o pregunta..."
            ></textarea>
          </div>

          <!-- Status Messages -->
          <div v-if="submitStatus === 'success'" class="status-message success">
            ✓ ¡Confirmación enviada exitosamente!
          </div>
          
          <div v-if="submitStatus === 'error'" class="status-message error">
            ✗ Hubo un error al enviar. Por favor intenta nuevamente.
          </div>

          <button 
            type="submit" 
            class="form-submit"
            :disabled="isSubmitting"
          >
            {{ isSubmitting ? 'Enviando...' : 'Enviar confirmación' }}
          </button>
        </form>
      </div>
    </div>
  </main>
</template>
