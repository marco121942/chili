<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { 
  Flame, 
  Sparkles, 
  Volume2, 
  VolumeX, 
  Play, 
  Pause, 
  MessageSquareQuote
} from "lucide-vue-next";
import videoSrc from "../assets/video/presentacion.mp4";

const videoRef = ref<HTMLVideoElement | null>(null);
const isMuted = ref(false);
const isPlaying = ref(true);

const toggleMute = () => {
  if (videoRef.value) {
    videoRef.value.muted = !videoRef.value.muted;
    isMuted.value = videoRef.value.muted;
  }
};

const togglePlay = () => {
  if (videoRef.value) {
    if (videoRef.value.paused) {
      videoRef.value.play();
      isPlaying.value = true;
    } else {
      videoRef.value.pause();
      isPlaying.value = false;
    }
  }
};

let interactionHandler: (() => void) | null = null;

onMounted(() => {
  if (videoRef.value) {
    videoRef.value.muted = false;
    videoRef.value.volume = 0.85;

    const playPromise = videoRef.value.play();
    if (playPromise !== undefined) {
      playPromise
        .then(() => {
          isPlaying.value = true;
          isMuted.value = false;
        })
        .catch(() => {
          // Si el navegador bloquea audio automático previo a interacción de usuario,
          // iniciamos silenciado y habilitamos el audio al primer toque/clic
          if (videoRef.value) {
            videoRef.value.muted = true;
            isMuted.value = true;
            videoRef.value.play().catch(() => {});

            interactionHandler = () => {
              if (videoRef.value) {
                videoRef.value.muted = false;
                isMuted.value = false;
                videoRef.value.play().catch(() => {});
              }
              cleanupInteraction();
            };

            window.addEventListener("click", interactionHandler, { once: true });
            window.addEventListener("keydown", interactionHandler, { once: true });
            window.addEventListener("touchstart", interactionHandler, { once: true });
          }
        });
    }
  }
});

const cleanupInteraction = () => {
  if (interactionHandler) {
    window.removeEventListener("click", interactionHandler);
    window.removeEventListener("keydown", interactionHandler);
    window.removeEventListener("touchstart", interactionHandler);
    interactionHandler = null;
  }
};

onUnmounted(() => {
  cleanupInteraction();
});
</script>

<template>
  <section id="experiencia" class="relative w-full max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 my-8 sm:my-14 select-none scroll-mt-28">
    
    <!-- RESPLANDOR DE FONDO CÁLIDO DE HACIENDA -->
    <div class="absolute -top-12 left-1/3 -translate-x-1/2 w-96 h-96 bg-amber-400/15 rounded-full blur-3xl pointer-events-none"></div>
    <div class="absolute -bottom-12 right-1/4 w-96 h-96 bg-orange-500/10 rounded-full blur-3xl pointer-events-none"></div>

    <!-- CONTENEDOR GRID CON ALTURA SINCRONIZADA (ITEMS-STRETCH) -->
    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 lg:gap-10 items-stretch">
      
      <!-- ==================== COLUMNA 1: VIDEO CINEMATOGRÁFICO DE PRESENTACIÓN ==================== -->
      <!-- Altura sincronizada con la columna derecha (Título + Cuadro) sin desbordar espacio -->
      <div class="lg:col-span-5 relative w-full h-full min-h-[380px] sm:min-h-[420px]">
        <div class="relative w-full h-full group">
          
          <!-- Halo luminoso cálido que emana del video -->
          <div class="absolute -inset-2 bg-gradient-to-tr from-[#FFB800]/25 via-[#EA580C]/20 to-[#E52521]/15 rounded-[32px] blur-xl opacity-75 group-hover:opacity-100 transition-opacity duration-500"></div>

          <!-- Marco contenedor del video con borde bruñido -->
          <div class="relative w-full h-full rounded-2xl sm:rounded-3xl overflow-hidden bg-[#140803] border-2 border-amber-400/35 shadow-[0_20px_50px_rgba(40,15,5,0.30)]">
            
            <!-- Etiqueta superior flotante -->
            <div class="absolute top-3.5 left-3.5 z-20 inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-black/75 backdrop-blur-md border border-amber-400/40 text-amber-300 text-[10px] sm:text-xs font-black uppercase tracking-wider shadow-lg">
              <Flame class="w-3 h-3 text-[#FFB800] fill-[#FFB800] animate-pulse" />
              <span>EXPERIENCIA EN VIVO</span>
            </div>

            <!-- Video Player HTML5 con Audio Activado -->
            <video
              ref="videoRef"
              :src="videoSrc"
              autoplay
              :muted="isMuted"
              loop
              playsinline
              class="absolute inset-0 w-full h-full object-cover object-[center_35%] cursor-pointer"
              @click="togglePlay"
            ></video>

            <!-- Controles flotantes en la parte inferior del video -->
            <div class="absolute bottom-3 inset-x-3 z-20 flex items-center justify-between px-3 py-2 rounded-2xl bg-black/70 backdrop-blur-md border border-white/15">
              
              <!-- Botón Play/Pausa -->
              <button
                @click.stop="togglePlay"
                :aria-label="isPlaying ? 'Pausar video' : 'Reproducir video'"
                class="flex items-center gap-1.5 text-white/90 hover:text-white transition-colors cursor-pointer text-xs font-bold"
              >
                <component :is="isPlaying ? Pause : Play" class="w-4 h-4 text-amber-400 fill-amber-400" />
                <span>{{ isPlaying ? 'Pausar' : 'Reproducir' }}</span>
              </button>

              <!-- Botón Sonido On/Off con distintivo activo -->
              <button
                @click.stop="toggleMute"
                :aria-label="isMuted ? 'Activar sonido' : 'Silenciar video'"
                class="flex items-center gap-1.5 px-3 py-1 rounded-full transition-all cursor-pointer text-xs font-bold shadow-md"
                :class="isMuted ? 'bg-white/15 hover:bg-white/25 text-white' : 'bg-gradient-to-r from-amber-400 to-orange-400 hover:brightness-110 text-[#140803] font-black'"
              >
                <component :is="isMuted ? VolumeX : Volume2" class="w-4 h-4" :class="isMuted ? 'text-amber-300' : 'text-[#140803]'" />
                <span>{{ isMuted ? 'Activar Sonido' : 'Sonido Activo' }}</span>
              </button>
            </div>

            <!-- Viñeta degradada sutil -->
            <div class="absolute inset-0 pointer-events-none bg-gradient-to-t from-black/50 via-transparent to-black/30"></div>
          </div>

        </div>
      </div>

      <!-- ==================== COLUMNA 2: TÍTULO + CUADRO COMPACTO SIN ESPACIO VACÍO ==================== -->
      <div class="lg:col-span-7 flex flex-col justify-center text-left">
        
        <!-- ENCABEZADO SUPERIOR: BADGE + TÍTULO -->
        <div class="mb-3 sm:mb-4">
          <!-- BADGE ESTILO DIÁLOGO DE BARRIO -->
          <div class="inline-flex items-center gap-2 px-3.5 py-1 rounded-full bg-[#FAF5EC] border border-[#D4A373]/60 shadow-[0_2px_8px_rgba(40,15,5,0.08)] mb-2.5">
            <MessageSquareQuote class="w-3.5 h-3.5 text-[#EA580C]" />
            <span class="text-[11px] sm:text-xs font-black font-['Syne'] uppercase tracking-wider text-[#3E1F08]">
              DIÁLOGO CON EL CHILI NEGRO
            </span>
            <Sparkles class="w-3 h-3 text-[#D97706]" />
          </div>

          <!-- TÍTULO MONUMENTAL -->
          <h2 class="text-3xl sm:text-4xl lg:text-[42px] font-black font-['Syne'] text-[#140803] uppercase tracking-tight leading-[1.02]">
            NO ES SOLO COMIDA, ES
            <span class="block bg-gradient-to-r from-[#B91C1C] via-[#EA580C] to-[#D97706] bg-clip-text text-transparent">
              PURA PASIÓN & FIESTA
            </span>
          </h2>
        </div>

        <!-- TARJETA ESTILO BURBUJA DE DIÁLOGO / PERGAMINO ARTESANAL (AJUSTADA AL CONTENIDO) -->
        <div class="relative w-full rounded-2xl sm:rounded-3xl bg-[#FAF5EC]/95 border border-[#D4A373]/55 p-6 sm:p-7 shadow-[0_10px_30px_rgba(40,15,5,0.08)] flex flex-col">
          
          <!-- Comillas decorativas gigantes de fondo -->
          <span class="absolute right-4 top-2 text-7xl font-serif text-[#C29A5B]/15 select-none pointer-events-none leading-none">“</span>

          <!-- Texto de diálogo del fundador / maestro taquero -->
          <div class="relative z-10 space-y-3.5 text-[#3A2213] text-sm sm:text-base leading-relaxed font-normal">
            <p class="font-medium text-[#1A0A03]">
              <strong class="font-bold text-[#EA580C]">¡Quihúbole compadre, bienvenido a nuestra mesa!</strong> 
              El Chili Negro nació de un viaje por las cantinas y esquinas más sabrosas de México, con una misión clara: traer esa vibra de barrio, música y brasas encendidas directamente a tu paladar.
            </p>
            <p>
              Aquí no escatimamos en nada: las tortillas se calientan al momento, el queso Oaxaca se funde hasta hacer costra dorada, el consomé de birria hierve a fuego lento por más de 8 horas y las micheladas las servimos en 
              <span class="font-bold text-[#140803] underline decoration-[#EA580C] decoration-2">tarros monumentales de 1 litro bien helados</span>.
            </p>
            <p class="text-xs sm:text-sm text-[#61381E] italic pt-1 border-t border-[#D4A373]/30">
              «Vente con amigos o en familia. Prepárate para chuparte los dedos y brindar como se debe.»
            </p>
          </div>

          <!-- Firma del equipo (inmediatamente después del texto, sin vacío) -->
          <div class="mt-5 pt-3.5 border-t border-[#D4A373]/30 flex items-center justify-between">
            <div class="flex items-center gap-2.5">
              <div class="w-8 h-8 rounded-full bg-[#140803] flex items-center justify-center text-amber-300 font-black text-xs font-['Syne']">
                CN
              </div>
              <div>
                <p class="text-xs font-bold text-[#140803]">Familia El Chili Negro</p>
                <p class="text-[10px] text-[#85532F]">Tradición, Fuego & Mezquite</p>
              </div>
            </div>
            <span class="text-[11px] font-black text-[#EA580C] uppercase tracking-wider">
              100% ARTESANAL
            </span>
          </div>

        </div>

      </div>

    </div>

  </section>
</template>

<style scoped>
/* Contenedor responsivo con sincronización de altura exacta */
</style>
