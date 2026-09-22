import { computed } from "vue";

interface Props {
  label: string;
  color: string;
  to: string;
  sway?: "sway-1" | "sway-2";
  delay?: string;
  isExternal?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  sway: "sway-1",
  delay: "0s",
  isExternal: false
});

// Cálculo dinámico para ajustar el tamaño del texto al ancho disponible de la banderilla
const typographyStyle = computed(() => {
  const len = props.label.length;
  if (len <= 8) {
    return { fontSize: '12px', letterSpacing: '0.08em' };
  } else if (len <= 10) {
    return { fontSize: '11px', letterSpacing: '0.06em' };
  } else if (len <= 12) {
    return { fontSize: '10px', letterSpacing: '0.04em' };
  } else {
    return { fontSize: '9px', letterSpacing: '0.02em' };
  }
});
</script>

<template>
  <a
    :href="to"
    :target="isExternal ? '_blank' : '_self'"
    :rel="isExternal ? 'noopener noreferrer' : ''"
    :class="[
      sway === 'sway-1' ? 'animate-sway-1' : 'animate-sway-2'
    ]"
    :style="{ animationDelay: delay }"
    class="papel-banderilla-wrapper group relative flex flex-col items-center cursor-pointer select-none"
  >
    <!-- ==================== OPCIÓN 1: BANDERILLA CON PINZAS DE MADERA RÚSTICA ==================== -->
    <div class="relative w-full filter drop-shadow-[0_12px_22px_rgba(0,0,0,0.92)]">
      
      <svg 
        viewBox="0 0 160 150" 
        class="w-full h-auto overflow-visible" 
        fill="none" 
        xmlns="http://www.w3.org/2000/svg"
      >
        <defs>
          <!-- Gradiente de Madera Rústica para las Pinzas -->
          <linearGradient id="woodPinGradDef" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#E9C49A" />
            <stop offset="40%" stop-color="#C89666" />
            <stop offset="85%" stop-color="#8F5426" />
            <stop offset="100%" stop-color="#5E3311" />
          </linearGradient>

          <!-- Resalte de madera frontal -->
          <linearGradient id="woodPinHighlightDef" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="#FDEBD0" />
            <stop offset="50%" stop-color="#D4A373" />
            <stop offset="100%" stop-color="#9C6644" />
          </linearGradient>

          <!-- Metal del resorte/muelle de la pinza -->
          <linearGradient id="springMetalGradDef" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop offset="0%" stop-color="#71717A" />
            <stop offset="50%" stop-color="#E4E4E7" />
            <stop offset="100%" stop-color="#3F3F46" />
          </linearGradient>
        </defs>

        <!-- 1. Borde Superior Reforzado de la Banderilla -->
        <rect 
          x="4" 
          y="18" 
          width="152" 
          height="8" 
          rx="1" 
          :fill="color" 
          filter="brightness(1.12)"
        />
        <!-- Pespunte artesanal de papel picado -->
        <line x1="6" y1="22" x2="154" y2="22" stroke="#000000" stroke-width="0.9" stroke-dasharray="3.5 2.5" opacity="0.35"/>

        <!-- 2. Cuerpo del papel picado con 3 festones inferiores ondeados -->
        <path 
          d="M 4 25 L 156 25 L 156 112 C 156 112, 140 142, 120 142 C 100 142, 90 118, 80 118 C 70 118, 60 142, 40 142 C 20 142, 4 112, 4 112 Z" 
          :fill="color" 
        />

        <!-- 3. Perforaciones troqueladas tradicionales de Papel Picado -->
        <g fill="#070006" opacity="0.95">
          <!-- Borde Superior de orificios -->
          <circle cx="16" cy="34" r="2.2"/>
          <circle cx="28" cy="34" r="2.2"/>
          <circle cx="40" cy="34" r="2.2"/>
          <circle cx="52" cy="34" r="2.2"/>
          <circle cx="64" cy="34" r="2.2"/>
          <circle cx="80" cy="34" r="2.2"/>
          <circle cx="96" cy="34" r="2.2"/>
          <circle cx="108" cy="34" r="2.2"/>
          <circle cx="120" cy="34" r="2.2"/>
          <circle cx="132" cy="34" r="2.2"/>
          <circle cx="144" cy="34" r="2.2"/>

          <!-- Borde Lateral Izquierdo -->
          <circle cx="14" cy="46" r="2.2"/>
          <circle cx="14" cy="58" r="2.2"/>
          <circle cx="14" cy="70" r="2.2"/>
          <circle cx="14" cy="82" r="2.2"/>
          <circle cx="14" cy="94" r="2.2"/>
          <circle cx="14" cy="106" r="2.2"/>

          <!-- Borde Lateral Derecho -->
          <circle cx="146" cy="46" r="2.2"/>
          <circle cx="146" cy="58" r="2.2"/>
          <circle cx="146" cy="70" r="2.2"/>
          <circle cx="146" cy="82" r="2.2"/>
          <circle cx="146" cy="94" r="2.2"/>
          <circle cx="146" cy="106" r="2.2"/>

          <!-- Volutas ornamentales superiores y corazoncito mexicano -->
          <path d="M 26 42 C 40 38, 52 48, 64 44" stroke="#070006" stroke-width="2.2" fill="none" stroke-linecap="round"/>
          <path d="M 134 42 C 120 38, 108 48, 96 44" stroke="#070006" stroke-width="2.2" fill="none" stroke-linecap="round"/>
          <path d="M 80 40 C 77 36, 72 36, 72 40 C 72 44, 80 49, 80 49 C 80 49, 88 44, 88 40 C 88 36, 83 36, 80 40 Z" fill="#070006"/>

          <!-- Festones florales troquelados en la base inferior -->
          <!-- Festón Izquierdo -->
          <ellipse cx="32" cy="126" rx="2.5" ry="6.5" transform="rotate(-25 32 126)"/>
          <ellipse cx="40" cy="129" rx="2.5" ry="7.5"/>
          <ellipse cx="48" cy="126" rx="2.5" ry="6.5" transform="rotate(25 48 126)"/>

          <!-- Festón Central -->
          <ellipse cx="72" cy="110" rx="2.2" ry="5.5" transform="rotate(-30 72 110)"/>
          <ellipse cx="80" cy="112" rx="2.2" ry="6.5"/>
          <ellipse cx="88" cy="110" rx="2.2" ry="5.5" transform="rotate(30 88 110)"/>

          <!-- Festón Derecho -->
          <ellipse cx="112" cy="126" rx="2.5" ry="6.5" transform="rotate(-25 112 126)"/>
          <ellipse cx="120" cy="129" rx="2.5" ry="7.5"/>
          <ellipse cx="128" cy="126" rx="2.5" ry="6.5" transform="rotate(25 128 126)"/>
        </g>

        <!-- 4. Ventana central para el nombre de la sección -->
        <rect x="22" y="52" width="116" height="48" rx="6" fill="#070006" opacity="0.40" stroke="#070006" stroke-width="1.2" stroke-dasharray="3 3"/>

        <!-- ==================== PINZAS DE MADERA RÚSTICAS QUE SUJETAN LA BANDERILLA ==================== -->
        
        <!-- PINZA IZQUIERDA (x ≈ 26) -->
        <g id="wooden-clip-left">
          <!-- Sombra proyectada por la pinza sobre el papel -->
          <ellipse cx="28" cy="30" rx="4.5" ry="2" fill="#000000" opacity="0.45" />
          
          <!-- Brazo trasero de madera -->
          <rect x="23" y="4" width="7" height="24" rx="1.2" fill="url(#woodPinGradDef)" stroke="#3E1F08" stroke-width="0.8" transform="rotate(-2 26 16)" />
          
          <!-- Ranura/Boca de agarre donde muerde la cuerda (Y=16) -->
          <rect x="22" y="14" width="9" height="3.5" rx="0.8" fill="#2E1705" opacity="0.75" />

          <!-- Brazo frontal de madera con relieve -->
          <rect x="24" y="5" width="5" height="22" rx="1" fill="url(#woodPinHighlightDef)" />
          
          <!-- Veta de madera rústica -->
          <line x1="25.5" y1="6" x2="25.5" y2="25" stroke="#7A3E12" stroke-width="0.6" stroke-dasharray="2 1.5" opacity="0.6" />

          <!-- Resorte Metálico Central que aprieta la cuerda -->
          <rect x="22.5" y="13.5" width="8" height="3" rx="1" fill="url(#springMetalGradDef)" stroke="#27272A" stroke-width="0.6" />
          <line x1="24" y1="15" x2="29" y2="15" stroke="#FFFFFF" stroke-width="0.6" stroke-linecap="round" opacity="0.8" />
        </g>

        <!-- PINZA DERECHA (x ≈ 134) -->
        <g id="wooden-clip-right">
          <!-- Sombra proyectada por la pinza sobre el papel -->
          <ellipse cx="132" cy="30" rx="4.5" ry="2" fill="#000000" opacity="0.45" />
          
          <!-- Brazo trasero de madera -->
          <rect x="130" y="4" width="7" height="24" rx="1.2" fill="url(#woodPinGradDef)" stroke="#3E1F08" stroke-width="0.8" transform="rotate(2 134 16)" />
          
          <!-- Ranura/Boca de agarre donde muerde la cuerda (Y=16) -->
          <rect x="129" y="14" width="9" height="3.5" rx="0.8" fill="#2E1705" opacity="0.75" />

          <!-- Brazo frontal de madera con relieve -->
          <rect x="131" y="5" width="5" height="22" rx="1" fill="url(#woodPinHighlightDef)" />
          
          <!-- Veta de madera rústica -->
          <line x1="133.5" y1="6" x2="133.5" y2="25" stroke="#7A3E12" stroke-width="0.6" stroke-dasharray="2 1.5" opacity="0.6" />

          <!-- Resorte Metálico Central que aprieta la cuerda -->
          <rect x="129.5" y="13.5" width="8" height="3" rx="1" fill="url(#springMetalGradDef)" stroke="#27272A" stroke-width="0.6" />
          <line x1="131" y1="15" x2="136" y2="15" stroke="#FFFFFF" stroke-width="0.6" stroke-linecap="round" opacity="0.8" />
        </g>

      </svg>

      <!-- ==================== SOLO TEXTO (DINÁMICO, AJUSTADO AL ANCHO DE LA BANDERILLA) ==================== -->
      <div class="absolute inset-0 pt-12 pb-6 px-3 flex items-center justify-center text-center">
        <span 
          :style="typographyStyle"
          class="font-black-display font-black text-white uppercase leading-tight drop-shadow-[0_2px_4px_rgba(0,0,0,0.95)] px-1 group-hover:scale-105 transition-transform duration-200"
        >
          {{ label }}
        </span>
      </div>

    </div>
  </a>
</template>

<style scoped>
.papel-banderilla-wrapper {
  transform-origin: top center;
  transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.papel-banderilla-wrapper:hover {
  transform: translateY(-4px) scale(1.05) rotate(1deg) !important;
  z-index: 50;
}
</style>