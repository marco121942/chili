<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { ChevronLeft, ChevronRight, Flame, Sparkles, MessageCircle } from "lucide-vue-next";

export interface PromoSlide {
  id: string;
  badge: string;
  titleLine1: string;
  titleLine2: string;
  subtitle: string;
  tagExtra?: string;
  foodBadge?: string;
  priceInteger: string;
  priceDecimals: string;
  originalPrice?: string;
  image: string;
  ctaText: string;
  bgGradient: string;
  accentGlow: string;
  disclaimer: string;
}

const slides: PromoSlide[] = [
  {
    id: "combo-mamalon",
    badge: "PROMO MONUMENTAL",
    titleLine1: "COMBO",
    titleLine2: "MAMALÓN",
    subtitle: "Tarro helado 1L con brocheta de camarones & cecina + 3 Quesabirrias al consomé",
    tagExtra: "PÍDELO CON CLAMATO PICANTE",
    foodBadge: "🦐 MAR & TIERRA 1L",
    priceInteger: "49",
    priceDecimals: ".90",
    originalPrice: "S/ 68.00",
    image: "https://images.unsplash.com/photo-1582106245687-cbb466a9f07f?auto=format&fit=crop&w=1000&q=80",
    ctaText: "PÍDELO AQUÍ",
    bgGradient: "from-[#8B0000] via-[#B91C1C] to-[#580000]",
    accentGlow: "#FF3838",
    disclaimer: "*Válido para consumo en salón y delivery. Imágenes referenciales."
  },
  {
    id: "taco-fest",
    badge: "PARA COMPARTIR (2-3 PERSONAS)",
    titleLine1: "TACO FEST",
    titleLine2: "NEGRO",
    subtitle: "10 Tacos al Pastor Negro con piña asada al mezquite + Guacamole monumental en molcajete de piedra",
    tagExtra: "INCLUYE TOTOPOS DE MAÍZ AZUL",
    foodBadge: "🌮 MAÍZ AZUL ARTESANAL",
    priceInteger: "59",
    priceDecimals: ".90",
    originalPrice: "S/ 78.00",
    image: "https://images.unsplash.com/photo-1551504734-5ee1c4a1479b?auto=format&fit=crop&w=1000&q=80",
    ctaText: "PÍDELO AQUÍ",
    bgGradient: "from-[#5B0E2D] via-[#7E1D4B] to-[#3B071E]",
    accentGlow: "#E4007C",
    disclaimer: "*Promoción válida de martes a domingo. Stock limitado por día."
  },
  {
    id: "birria-brutal",
    badge: "EL MÁS PEDIDO DE LA CASA",
    titleLine1: "BIRRIA",
    titleLine2: "BRUTAL",
    subtitle: "4 Quesabirrias crujientes con costra de queso Oaxaca + Doble consomé hirviendo + Michelada 1L Ojo Rojo",
    tagExtra: "ALL YOU CAN DIP",
    foodBadge: "🧀 COSTRA DE QUESO OAXACA",
    priceInteger: "44",
    priceDecimals: ".90",
    originalPrice: "S/ 58.00",
    image: "https://images.unsplash.com/photo-1565299585323-38d6b0865b47?auto=format&fit=crop&w=1000&q=80",
    ctaText: "PÍDELO AQUÍ",
    bgGradient: "from-[#9A3412] via-[#C2410C] to-[#631E05]",
    accentGlow: "#FF6B00",
    disclaimer: "*Incluye limones, cebollitas cambray asadas y salsas artesanales."
  },
  {
    id: "volcan-supreme",
    badge: "COMBO CHELERO & BOTANERO",
    titleLine1: "VOLCÁN",
    titleLine2: "SUPREME",
    subtitle: "Montaña volcánica de totopos con birria, abundante cheddar fundido & jalapeños + 2 Micheladas 1L",
    tagExtra: "RACIÓN GIGANTE 2 LITROS",
    foodBadge: "🔥 VOLCÁN CON CHEDDAR FUNDIDO",
    priceInteger: "64",
    priceDecimals: ".90",
    originalPrice: "S/ 82.00",
    image: "https://images.unsplash.com/photo-1513456852971-30c0b8199d4d?auto=format&fit=crop&w=1000&q=80",
    ctaText: "PÍDELO AQUÍ",
    bgGradient: "from-[#854D0E] via-[#A16207] to-[#4D2800]",
    accentGlow: "#FFB800",
    disclaimer: "*Ideal para disfrutar con amigos. Válido todo el horario de atención."
  },
  {
    id: "noches-mezcal",
    badge: "HAPPY HOUR CANTINERO",
    titleLine1: "NOCHES",
    titleLine2: "2X1 EN TRAGOS",
    subtitle: "2x1 en Margaritas Maracuyá flameadas con jalapeño & Mezcalitas ahumadas de frutos rojos silvestres",
    tagExtra: "TEQUILA 100% AGAVE & MEZCAL OAXACA",
    foodBadge: "🍹 TEQUILA 100% DE AGAVE",
    priceInteger: "36",
    priceDecimals: ".00",
    originalPrice: "2x1",
    image: "https://images.unsplash.com/photo-1556881286-fc6915169721?auto=format&fit=crop&w=1000&q=80",
    ctaText: "PÍDELO AQUÍ",
    bgGradient: "from-[#881337] via-[#9F1239] to-[#4C0519]",
    accentGlow: "#F43F5E",
    disclaimer: "*Aplica de 6:00 pm a 11:00 pm. Solo para mayores de 18 años."
  }
];

const currentIndex = ref(0);
const isPaused = ref(false);
let autoPlayTimer: ReturnType<typeof setInterval> | null = null;

const nextSlide = () => {
  currentIndex.value = (currentIndex.value + 1) % slides.length;
};

const prevSlide = () => {
  currentIndex.value = (currentIndex.value - 1 + slides.length) % slides.length;
};

const goToSlide = (idx: number) => {
  currentIndex.value = idx;
};

// Cálculo de estados de posición 3D (Coverflow con cards asomadas a los lados)
const getSlideState = (idx: number) => {
  const total = slides.length;
  let diff = (idx - currentIndex.value) % total;
  if (diff > total / 2) diff -= total;
  if (diff < -total / 2) diff += total;

  if (diff === 0) {
    return {
      style: {
        transform: "translateX(-50%) scale(1)",
        zIndex: 30,
        opacity: 1,
        pointerEvents: "auto" as const,
        filter: "none"
      },
      isCenter: true,
      diff: 0
    };
  }

  if (diff === -1) {
    return {
      style: {
        transform: "translateX(calc(-50% - 94% - 18px)) scale(0.88) perspective(1000px) rotateY(3.5deg)",
        zIndex: 20,
        opacity: 0.65,
        pointerEvents: "auto" as const,
        filter: "brightness(0.9) saturate(0.95)"
      },
      isCenter: false,
      diff: -1
    };
  }

  if (diff === 1) {
    return {
      style: {
        transform: "translateX(calc(-50% + 94% + 18px)) scale(0.88) perspective(1000px) rotateY(-3.5deg)",
        zIndex: 20,
        opacity: 0.65,
        pointerEvents: "auto" as const,
        filter: "brightness(0.9) saturate(0.95)"
      },
      isCenter: false,
      diff: 1
    };
  }

  const direction = diff > 0 ? 1 : -1;
  return {
    style: {
      transform: `translateX(calc(-50% + ${direction * 185}%)) scale(0.72)`,
      zIndex: 10,
      opacity: 0,
      pointerEvents: "none" as const,
      filter: "brightness(0.6)"
    },
    isCenter: false,
    diff
  };
};

const onCardClick = (idx: number, state: ReturnType<typeof getSlideState>) => {
  if (!state.isCenter) {
    goToSlide(idx);
  }
};

const startAutoPlay = () => {
  stopAutoPlay();
  autoPlayTimer = setInterval(() => {
    if (!isPaused.value) {
      nextSlide();
    }
  }, 5000);
};

const stopAutoPlay = () => {
  if (autoPlayTimer) {
    clearInterval(autoPlayTimer);
    autoPlayTimer = null;
  }
};

const handleOrder = (slide: PromoSlide, e?: Event) => {
  if (e) e.stopPropagation();
  const text = `¡Hola El Chili Negro! Quiero pedir la promo: ${slide.titleLine1} ${slide.titleLine2} (S/ ${slide.priceInteger}${slide.priceDecimals}).`;
  window.open(`https://wa.me/51900514721?text=${encodeURIComponent(text)}`, "_blank");
};

// Soporte para gestos táctiles (Swipe)
let touchStartX = 0;
let touchEndX = 0;

const onTouchStart = (e: TouchEvent) => {
  touchStartX = e.changedTouches[0].screenX;
};

const onTouchEnd = (e: TouchEvent) => {
  touchEndX = e.changedTouches[0].screenX;
  if (touchStartX - touchEndX > 50) {
    nextSlide();
  } else if (touchEndX - touchStartX > 50) {
    prevSlide();
  }
};

onMounted(() => {
  startAutoPlay();
});

onUnmounted(() => {
  stopAutoPlay();
});
</script>

<template>
  <section class="relative w-full max-w-[1520px] mx-auto px-2 sm:px-4 my-4 sm:my-8 select-none overflow-hidden">
    
    <!-- ENCABEZADO FESTIVO ARTESANAL SUPERIOR -->
    <div class="flex items-center justify-center gap-3 mb-4 sm:mb-6">
      <div class="h-[1px] w-12 sm:w-24 bg-gradient-to-r from-transparent via-[#C29A5B] to-transparent opacity-60"></div>
      <div class="inline-flex items-center gap-2 px-4 py-1.5 rounded-full bg-[#FAF5EC] border border-[#D4A373]/50 shadow-[0_2px_8px_rgba(40,15,5,0.08)]">
        <Flame class="w-4 h-4 text-[#EA580C] fill-[#EA580C] animate-pulse" />
        <span class="text-xs sm:text-sm font-black font-['Syne'] uppercase tracking-wider text-[#3E1F08]">
          OFERTAS MONUMENTALES • EDICIÓN LIMITADA
        </span>
        <Sparkles class="w-3.5 h-3.5 text-[#D97706]" />
      </div>
      <div class="h-[1px] w-12 sm:w-24 bg-gradient-to-r from-transparent via-[#C29A5B] to-transparent opacity-60"></div>
    </div>

    <!-- ESCENARIO 3D DEL CARRUSEL PANORÁMICO -->
    <div
      class="relative w-full h-[540px] sm:h-[460px] md:h-[430px]"
      @mouseenter="isPaused = true"
      @mouseleave="isPaused = false"
      @touchstart="onTouchStart"
      @touchend="onTouchEnd"
    >
      
      <!-- CONTENEDOR DE CARDS 3D -->
      <div
        v-for="(slide, idx) in slides"
        :key="slide.id"
        :style="getSlideState(idx).style"
        @click="onCardClick(idx, getSlideState(idx))"
        :class="[
          'absolute top-0 left-1/2 w-[88vw] sm:w-[80vw] md:w-[72vw] max-w-[1040px] h-full rounded-2xl sm:rounded-3xl overflow-hidden transition-all duration-600 ease-[cubic-bezier(0.22,1,0.36,1)]',
          getSlideState(idx).isCenter
            ? 'shadow-[0_25px_60px_rgba(45,18,6,0.40)] border-2 border-amber-400/40 cursor-default'
            : 'shadow-[0_15px_35px_rgba(45,18,6,0.25)] border border-white/20 cursor-pointer hover:opacity-85'
        ]"
      >
        <!-- FONDO CON GRADIENTE DE ALTO IMPACTO -->
        <div :class="['absolute inset-0 w-full h-full bg-gradient-to-r', slide.bgGradient]">
          <!-- Marca de agua artesanal de grecas mexicanas -->
          <div class="absolute inset-0 opacity-[0.07] pointer-events-none bg-[radial-gradient(#FFFFFF_1.5px,transparent_1.5px)] [background-size:24px_24px]"></div>
          
          <!-- Brillo de resplandor festivo en esquinas -->
          <div class="absolute -top-24 -right-24 w-72 h-72 rounded-full blur-3xl opacity-30 pointer-events-none bg-amber-400"></div>
          <div class="absolute -bottom-24 -left-24 w-72 h-72 rounded-full blur-3xl opacity-25 pointer-events-none bg-orange-600"></div>

          <!-- Borde interior fino con reflejo dorado -->
          <div class="absolute inset-0 rounded-2xl sm:rounded-3xl border border-white/15 pointer-events-none"></div>

          <!-- CONTENIDO INTERNO: 3 COLUMNAS -->
          <div class="relative z-10 h-full p-5 sm:p-8 md:p-10 flex flex-col justify-between">
            
            <div class="grid grid-cols-1 md:grid-cols-12 gap-3 sm:gap-5 items-center flex-1">
              
              <!-- COLUMNA IZQUIERDA: TÍTULO MONUMENTAL & DETALLES -->
              <div class="md:col-span-5 flex flex-col items-start justify-center text-left pr-0 md:pr-2">
                <!-- Badge superior -->
                <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-black/45 backdrop-blur-md border border-amber-400/40 text-amber-300 text-[10px] sm:text-xs font-black uppercase tracking-wider mb-2 sm:mb-2.5 shadow-md">
                  <Flame class="w-3 h-3 text-[#FFB800] fill-[#FFB800]" />
                  <span>{{ slide.badge }}</span>
                </div>

                <!-- Título Monumental -->
                <h2 class="text-3xl sm:text-4xl lg:text-5xl font-black font-['Syne'] tracking-tight text-white uppercase leading-[0.92] drop-shadow-[0_4px_16px_rgba(0,0,0,0.7)]">
                  <span>{{ slide.titleLine1 }}</span>
                  <br />
                  <span class="text-white drop-shadow-[0_6px_20px_rgba(0,0,0,0.85)]">{{ slide.titleLine2 }}</span>
                </h2>

                <!-- Subtítulo -->
                <p class="text-white/90 text-xs sm:text-sm font-medium leading-snug mt-2 sm:mt-3 max-w-xs sm:max-w-sm drop-shadow-sm">
                  {{ slide.subtitle }}
                </p>

                <!-- Tag extra -->
                <div
                  v-if="slide.tagExtra"
                  class="mt-2.5 sm:mt-3 inline-flex items-center gap-1.5 px-2.5 py-1 rounded-md bg-black/60 border border-amber-400/30 text-[10px] sm:text-[11px] font-black uppercase text-amber-300 tracking-wide shadow-sm"
                >
                  <Sparkles class="w-3 h-3 text-[#FFB800]" />
                  <span>{{ slide.tagExtra }}</span>
                </div>
              </div>

              <!-- COLUMNA CENTRAL: PLATILLO HEROICO CON VIDA -->
              <div class="md:col-span-4 flex items-center justify-center relative my-1 md:my-0">
                <!-- Halo de fuego y calor de comal -->
                <div class="absolute w-48 h-48 sm:w-60 sm:h-60 md:w-64 md:h-64 bg-amber-400/25 rounded-full blur-2xl pointer-events-none"></div>

                <!-- Contenedor del platillo -->
                <div class="group relative w-44 h-44 sm:w-56 sm:h-56 md:w-60 md:h-60 lg:w-68 lg:h-68 rounded-2xl sm:rounded-3xl overflow-hidden shadow-[0_20px_50px_rgba(0,0,0,0.85)] border-2 border-white/30 transform transition-transform duration-500 hover:scale-105">
                  <img
                    :src="slide.image"
                    :alt="slide.titleLine1 + ' ' + slide.titleLine2"
                    class="w-full h-full object-cover object-center"
                    loading="lazy"
                  />
                  <!-- Sombra y viñeta apetitosa -->
                  <div class="absolute inset-0 bg-gradient-to-t from-black/55 via-transparent to-black/15"></div>

                  <!-- Badge flotante sobre la foto -->
                  <div
                    v-if="slide.foodBadge"
                    class="absolute top-2.5 left-2.5 px-2.5 py-1 rounded-full bg-black/75 backdrop-blur-md border border-white/20 text-[9px] sm:text-[10px] font-black uppercase text-white tracking-wider shadow-lg flex items-center gap-1"
                  >
                    <span>{{ slide.foodBadge }}</span>
                  </div>
                </div>
              </div>

              <!-- COLUMNA DERECHA: PRECIO GIGANTE + BOTÓN DORADO PÍDELO AQUÍ -->
              <div class="md:col-span-3 flex flex-col items-center md:items-end justify-center text-center md:text-right pl-0 md:pl-2">
                
                <!-- Precio anterior tachado -->
                <span
                  v-if="slide.originalPrice"
                  class="text-xs sm:text-sm text-white/75 line-through font-bold tracking-wide"
                >
                  Antes {{ slide.originalPrice }}
                </span>

                <!-- Bloque de Precio Gigante (S/ 49.90) -->
                <div class="flex items-start justify-center md:justify-end text-white my-0.5 sm:my-1">
                  <span class="text-xl sm:text-2xl font-black font-['Syne'] mr-1 mt-1 text-amber-300">S/</span>
                  <span class="text-5xl sm:text-6xl lg:text-7xl font-black font-['Syne'] tracking-tighter leading-none text-white drop-shadow-[0_4px_16px_rgba(0,0,0,0.6)]">
                    {{ slide.priceInteger }}
                  </span>
                  <span class="text-2xl sm:text-3xl font-black font-['Syne'] mt-1 text-amber-300">
                    {{ slide.priceDecimals }}
                  </span>
                </div>

                <!-- Botón de Acción Monumental (Degradado Dorado de Fuego y Maíz) -->
                <button
                  @click="handleOrder(slide, $event)"
                  class="mt-2.5 sm:mt-3.5 px-7 sm:px-9 py-3 sm:py-3.5 rounded-xl font-black font-['Syne'] text-xs sm:text-sm text-[#140803] uppercase tracking-wider bg-gradient-to-r from-[#FFB800] via-[#F59E0B] to-[#EA580C] hover:brightness-110 active:scale-95 transition-all duration-200 shadow-[0_6px_22px_rgba(234,88,12,0.45)] hover:shadow-[0_8px_30px_rgba(234,88,12,0.65)] hover:scale-105 cursor-pointer flex items-center justify-center gap-2"
                >
                  <MessageCircle class="w-4 h-4 text-[#140803] fill-[#140803]" />
                  <span>{{ slide.ctaText }}</span>
                </button>

                <!-- Disclaimer legal pequeño -->
                <span class="text-[9px] text-white/65 font-light mt-2.5 block max-w-[200px]">
                  {{ slide.disclaimer }}
                </span>
              </div>

            </div>

          </div>
        </div>

        <!-- CAPA TRANSLÚCIDA PARA CARDS LATERALES (HACER CLIC PARA TRAER AL CENTRO) -->
        <div
          v-if="!getSlideState(idx).isCenter"
          class="absolute inset-0 bg-black/20 hover:bg-transparent transition-colors duration-300 pointer-events-none flex items-center justify-center"
        >
          <span class="sr-only">Ver esta promoción</span>
        </div>
      </div>

      <!-- BOTONES DE NAVEGACIÓN (CHEVRONS LATERALES FLOTANTES) -->
      <button
        @click.stop="prevSlide"
        aria-label="Promoción anterior"
        class="absolute left-2 sm:left-6 top-1/2 -translate-y-1/2 z-40 w-10 h-10 sm:w-12 sm:h-12 rounded-full bg-[#FAF5EC]/90 hover:bg-[#FAF5EC] border border-[#D4A373] text-[#3E1F08] flex items-center justify-center shadow-[0_4px_16px_rgba(40,15,5,0.2)] transition-all duration-200 hover:scale-110 cursor-pointer"
      >
        <ChevronLeft class="w-5 h-5 sm:w-6 sm:h-6" />
      </button>

      <button
        @click.stop="nextSlide"
        aria-label="Promoción siguiente"
        class="absolute right-2 sm:right-6 top-1/2 -translate-y-1/2 z-40 w-10 h-10 sm:w-12 sm:h-12 rounded-full bg-[#FAF5EC]/90 hover:bg-[#FAF5EC] border border-[#D4A373] text-[#3E1F08] flex items-center justify-center shadow-[0_4px_16px_rgba(40,15,5,0.2)] transition-all duration-200 hover:scale-110 cursor-pointer"
      >
        <ChevronRight class="w-5 h-5 sm:w-6 sm:h-6" />
      </button>

    </div>

    <!-- INDICADORES DE PUNTOS (DOTS) CON DISEÑO DE ALTA GAMA -->
    <div class="flex items-center justify-center gap-2 mt-4 sm:mt-6">
      <button
        v-for="(slide, idx) in slides"
        :key="'dot-' + slide.id"
        @click="goToSlide(idx)"
        :aria-label="`Ir a la promo ${idx + 1}`"
        :class="[
          'transition-all duration-300 rounded-full cursor-pointer',
          currentIndex === idx
            ? 'w-8 h-2.5 bg-gradient-to-r from-[#FFB800] to-[#EA580C] shadow-[0_2px_8px_rgba(234,88,12,0.4)]'
            : 'w-2.5 h-2.5 bg-[#D4A373]/40 hover:bg-[#D4A373]/70'
        ]"
      ></button>
    </div>

  </section>
</template>

<style scoped>
/* Las transiciones 3D de las cards están gestionadas suavemente por hardware con transition-all */
</style>
