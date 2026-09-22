<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from "vue";
import logoImg from "@/assets/img/logo.png";

const isScrolled = ref(false);

const handleScroll = () => {
  if (typeof window === "undefined") return;
  isScrolled.value = window.scrollY > 20;
};

onMounted(() => {
  handleScroll();
  window.addEventListener("scroll", handleScroll, { passive: true });
});

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
});

interface NavItem {
  id: string;
  label: string;
  color: string;
  to: string;
  x: number;
  y: number;
  angle: number;
  swayClass: string;
  isExternal?: boolean;
}

// 6 Secciones de navegación con cálculo de catenaria y colores oficiales
// Lado izquierdo: 1. Presentación, 2. Platos, 3. Ubicación
// Lado derecho:   1. Presentación, 2. Platos, 3. Ubicación
const navItems: NavItem[] = [
  {
    id: "presentacion-izq",
    label: "PRESENTACIÓN",
    color: "#00A86B", // Verde Agave
    to: "#experiencia",
    x: 145,
    y: 38,
    angle: 4.5,
    swayClass: "sway-flag-1"
  },
  {
    id: "platos-izq",
    label: "PLATOS",
    color: "#FFB800", // Amarillo Cempasúchil
    to: "#carta",
    x: 335,
    y: 58,
    angle: 2.5,
    swayClass: "sway-flag-2"
  },
  {
    id: "ubicacion-izq",
    label: "UBICACIÓN",
    color: "#E4007C", // Rosa Mexicano
    to: "#ubicacion",
    x: 525,
    y: 73,
    angle: 0.8,
    swayClass: "sway-flag-1"
  },
  {
    id: "presentacion-der",
    label: "PRESENTACIÓN",
    color: "#FF6B00", // Naranja Fiesta
    to: "#experiencia",
    x: 915,
    y: 73,
    angle: -0.8,
    swayClass: "sway-flag-2"
  },
  {
    id: "platos-der",
    label: "PLATOS",
    color: "#00B4D8", // Azul Turquesa
    to: "#carta",
    x: 1105,
    y: 58,
    angle: -2.5,
    swayClass: "sway-flag-1"
  },
  {
    id: "ubicacion-der",
    label: "UBICACIÓN",
    color: "#CE1126", // Rojo Bandera
    to: "#ubicacion",
    x: 1295,
    y: 38,
    angle: -4.5,
    swayClass: "sway-flag-2"
  }
];

interface BanderillaMetrics {
  fontSize: number;
  letterSpacing: string;
  width: number;
  halfWidth: number;
  windowWidth: number;
  maxTextLength: number;
  textLengthClamp?: number;
  leftPinX: number;
  rightPinX: number;
  path: string;
  shadowPath: string;
  topHoles: number[];
  leftHoleX: number;
  rightHoleX: number;
  leftVoluta: string;
  rightVoluta: string;
  leftClusterCenter: number;
  rightClusterCenter: number;
}

/**
 * Ponderaciones tipográficas reales medidas con la fuente Syne 800 en Chromium:
 * Cada carácter mayúscula ocupa ~1.05em en promedio, con espacios e 'I' más angostos (~0.45em)
 * y letras 'M', 'W' más anchas (~1.35em).
 */
function getBanderillaMetrics(label: string): BanderillaMetrics {
  let emUnits = 0;
  for (const char of label.toUpperCase()) {
    if (char === ' ' || char === 'I' || char === '1' || char === '|' || char === '.') {
      emUnits += 0.45;
    } else if (char === 'M' || char === 'W' || char === '—') {
      emUnits += 1.35;
    } else if (char === 'E' || char === 'F' || char === 'L' || char === 'T' || char === 'J') {
      emUnits += 0.85;
    } else {
      emUnits += 1.05;
    }
  }

  // Tamaño de texto grande, nítido y consistente
  const fontSize = 9.8;
  const letterSpacingEm = 0.06;
  const totalEm = emUnits + (label.length - 1) * letterSpacingEm;
  const textWidth = Math.round(totalEm * fontSize);

  // El rectángulo oscuro contiene holgadamente la palabra (mínimo 14px a cada lado)
  const windowWidth = Math.max(116, textWidth + 28);
  // La banderilla completa se expande para enmarcar el contenedor (12px de papel a cada lado)
  const width = Math.max(140, windowWidth + 24);
  const halfWidth = width / 2;

  // Límite estricto infranqueable: de este ancho interno el texto jamás puede pasar
  const maxTextLength = windowWidth - 12;
  const textLengthClamp = textWidth > maxTextLength ? maxTextLength : undefined;

  // Pinzas posicionadas uniformemente respecto a los bordes de la banderilla
  const leftPinX = -halfWidth + 20;
  const rightPinX = halfWidth - 20;

  // Curvas festoneadas proporcionales al nuevo ancho
  const p1 = (halfWidth * 0.79).toFixed(1);
  const p2 = (halfWidth * 0.53).toFixed(1);
  const p3 = (halfWidth * 0.26).toFixed(1);
  const p4 = (halfWidth * 0.13).toFixed(1);
  const np1 = (-halfWidth * 0.79).toFixed(1);
  const np2 = (-halfWidth * 0.53).toFixed(1);
  const np3 = (-halfWidth * 0.26).toFixed(1);
  const np4 = (-halfWidth * 0.13).toFixed(1);

  const path = `M ${-halfWidth} 8 L ${halfWidth} 8 L ${halfWidth} 84 C ${halfWidth} 84, ${p1} 108, ${p2} 108 C ${p3} 108, ${p4} 88, 0 88 C ${np4} 88, ${np3} 108, ${np2} 108 C ${np1} 108, ${-halfWidth} 84, ${-halfWidth} 84 Z`;
  const shadowPath = `M ${-halfWidth} 4 L ${halfWidth} 4 L ${halfWidth} 84 C ${halfWidth} 84, ${p1} 108, ${p2} 108 C ${p3} 108, ${p4} 88, 0 88 C ${np4} 88, ${np3} 108, ${np2} 108 C ${np1} 108, ${-halfWidth} 84, ${-halfWidth} 84 Z`;

  // Orificios superiores distribuidos a lo largo del ancho
  const topHoles: number[] = [0];
  for (let x = 20; x <= halfWidth - 10; x += 12) {
    topHoles.push(x);
    topHoles.push(-x);
  }
  topHoles.sort((a, b) => a - b);

  // Orificios laterales
  const leftHoleX = -halfWidth + 8;
  const rightHoleX = halfWidth - 8;

  // Volutas decorativas superiores
  const leftVoluta = `M ${(-halfWidth * 0.7).toFixed(1)} 23 C ${(-halfWidth * 0.52).toFixed(1)} 19, ${(-halfWidth * 0.35).toFixed(1)} 28, -12 24`;
  const rightVoluta = `M ${(halfWidth * 0.7).toFixed(1)} 23 C ${(halfWidth * 0.52).toFixed(1)} 19, ${(halfWidth * 0.35).toFixed(1)} 28, 12 24`;

  // Centros de festones florales inferiores
  const leftClusterCenter = -halfWidth * 0.53;
  const rightClusterCenter = halfWidth * 0.53;

  return {
    fontSize,
    letterSpacing: `${letterSpacingEm}em`,
    width,
    halfWidth,
    windowWidth,
    maxTextLength,
    textLengthClamp,
    leftPinX,
    rightPinX,
    path,
    shadowPath,
    topHoles,
    leftHoleX,
    rightHoleX,
    leftVoluta,
    rightVoluta,
    leftClusterCenter,
    rightClusterCenter
  };
}

const flags = computed(() => {
  return navItems.map(item => ({
    ...item,
    metrics: getBanderillaMetrics(item.label)
  }));
});
</script>

<template>
  <header class="relative w-full overflow-visible z-50 pt-0 select-none pointer-events-none">
    <!-- Fondo protector sutil cuando el usuario hace scroll para máxima legibilidad sobre cualquier foto -->
    <div
      :class="[
        'absolute inset-x-0 top-0 h-28 sm:h-36 pointer-events-none transition-all duration-500 ease-out',
        isScrolled
          ? 'bg-gradient-to-b from-[#FAF5EC]/95 via-[#F2E8D7]/80 to-transparent backdrop-blur-[4px]'
          : 'bg-transparent'
      ]"
    ></div>

    <div class="relative w-full max-w-[1440px] mx-auto px-1 sm:px-4 pointer-events-none">
      
      <!-- ==================== SISTEMA SVG UNIFICADO: CUERDA + PINZAS + BANDERILLAS + LOGO ==================== -->
      <svg 
        viewBox="0 0 1440 185" 
        class="w-full h-auto overflow-visible filter drop-shadow-[0_6px_14px_rgba(45,18,6,0.22)] pointer-events-none" 
        fill="none" 
        xmlns="http://www.w3.org/2000/svg"
      >
        <defs>
          <!-- Dintel o Viga Rústica de Mezquite Superior -->
          <linearGradient id="woodBeamGrad" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="#381D0E" />
            <stop offset="40%" stop-color="#4E2814" />
            <stop offset="80%" stop-color="#2D1509" />
            <stop offset="100%" stop-color="#1A0A03" />
          </linearGradient>

          <!-- Máscara de recorte que elimina residuos y sombra superior de logo.png -->
          <clipPath id="logoCleanClip">
            <rect x="625" y="66" width="190" height="165" />
          </clipPath>

          <!-- Gradiente de Cuerda de Yute Rústica -->
          <linearGradient id="ropeGrad" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="#EED7B7" />
            <stop offset="45%" stop-color="#C28D58" />
            <stop offset="85%" stop-color="#784218" />
            <stop offset="100%" stop-color="#462208" />
          </linearGradient>

          <!-- Clavos en los extremos -->
          <radialGradient id="nailGrad" cx="35%" cy="35%" r="65%">
            <stop offset="0%" stop-color="#A1A1AA" />
            <stop offset="60%" stop-color="#3F3F46" />
            <stop offset="100%" stop-color="#18181B" />
          </radialGradient>

          <!-- Madera - Parte Trasera de la Pinza -->
          <linearGradient id="pinWoodBack" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#C89666" />
            <stop offset="100%" stop-color="#603410" />
          </linearGradient>

          <!-- Madera - Parte Frontal con Relieve -->
          <linearGradient id="pinWoodFront" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="#FDEBD0" />
            <stop offset="45%" stop-color="#D4A373" />
            <stop offset="100%" stop-color="#8F5426" />
          </linearGradient>

          <!-- Resorte Metálico de la Pinza -->
          <linearGradient id="pinSpring" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop offset="0%" stop-color="#71717A" />
            <stop offset="50%" stop-color="#FFFFFF" />
            <stop offset="100%" stop-color="#27272A" />
          </linearGradient>

          <!-- Resplandor del Logo Central -->
          <radialGradient id="logoGlow" cx="50%" cy="50%" r="50%">
            <stop offset="0%" stop-color="#FFB800" stop-opacity="0.8" />
            <stop offset="100%" stop-color="#FFB800" stop-opacity="0" />
          </radialGradient>
        </defs>

        <!-- ==================== CAPA 0: DINTEL / VIGA RÚSTICA SUPERIOR ==================== -->
        <g id="ceiling-beam" class="pointer-events-none">
          <rect x="0" y="0" width="1440" height="5" fill="url(#woodBeamGrad)" />
          <line x1="0" y1="5" x2="1440" y2="5" stroke="#120500" stroke-width="0.8" opacity="0.45" />
          <line x1="0" y1="1.5" x2="1440" y2="1.5" stroke="#844722" stroke-width="0.6" stroke-dasharray="8 6" opacity="0.25" />
        </g>

        <!-- ==================== CAPA 2: BANDERILLAS COMPLETAS (SIN CORTES NI LÍNEAS NEGRAS) ==================== -->
        <g id="papel-picado-flags-layer">
          <template v-for="flag in flags" :key="'flag-' + flag.id">
            <a 
              :href="flag.to" 
              :target="flag.isExternal ? '_blank' : '_self'"
              :rel="flag.isExternal ? 'noopener noreferrer' : ''"
              class="flag-link-item pointer-events-auto"
            >
              <!-- GRUPO 1: Posicionamiento inmutable en la soga -->
              <g :transform="`translate(${flag.x}, ${flag.y}) rotate(${flag.angle})`">
                
                <!-- 1. Brazo Trasero de las Pinzas (Posición adaptada al ancho dinámico) -->
                <rect :x="flag.metrics.leftPinX - 4" y="-12" width="8" height="26" rx="1.2" fill="url(#pinWoodBack)" stroke="#3E1F08" stroke-width="0.8" />
                <rect :x="flag.metrics.rightPinX - 4" y="-12" width="8" height="26" rx="1.2" fill="url(#pinWoodBack)" stroke="#3E1F08" stroke-width="0.8" />

                <!-- 2. LÁMINA CONTINUA DE PAPEL PICADO (Ancho calculado dinámicamente según la palabra) -->
                <!-- El pivote está en Y=4px (entre las pinzas), por lo que la parte superior NO se mueve de la soga -->
                <g class="paper-seamless-sheet">
                  
                  <!-- Sombra proyectada suave y cálida sobre el fondo pergamino -->
                  <path 
                    :d="flag.metrics.shadowPath" 
                    fill="#3A1C0A" 
                    opacity="0.20" 
                    transform="translate(0, 4)"
                  />

                  <!-- Borde Superior Reforzado del Papel -->
                  <rect :x="-flag.metrics.halfWidth" y="2" :width="flag.metrics.width" height="7" rx="1" :fill="flag.color" filter="brightness(1.15)" />
                  <line :x1="-flag.metrics.halfWidth + 2" y1="5" :x2="flag.metrics.halfWidth - 2" y2="5" stroke="#000000" stroke-width="0.8" stroke-dasharray="3 2" opacity="0.25" />

                  <!-- Cuerpo Entero Continuo del Papel Picado con festones adaptados al ancho -->
                  <path 
                    :d="flag.metrics.path" 
                    :fill="flag.color" 
                  />

                  <!-- Troquelados artesanales tradicionales (calados hacia el fondo pergamino) -->
                  <g fill="#FAF5EC" opacity="0.95">
                    <!-- Borde Superior de orificios distribuidos en todo el ancho -->
                    <circle v-for="cx in flag.metrics.topHoles" :key="cx" :cx="cx" cy="16" r="1.8"/>

                    <!-- Borde Lateral Izquierdo -->
                    <circle :cx="flag.metrics.leftHoleX" cy="27" r="1.8"/>
                    <circle :cx="flag.metrics.leftHoleX" cy="38" r="1.8"/>
                    <circle :cx="flag.metrics.leftHoleX" cy="49" r="1.8"/>
                    <circle :cx="flag.metrics.leftHoleX" cy="60" r="1.8"/>
                    <circle :cx="flag.metrics.leftHoleX" cy="71" r="1.8"/>

                    <!-- Borde Lateral Derecho -->
                    <circle :cx="flag.metrics.rightHoleX" cy="27" r="1.8"/>
                    <circle :cx="flag.metrics.rightHoleX" cy="38" r="1.8"/>
                    <circle :cx="flag.metrics.rightHoleX" cy="49" r="1.8"/>
                    <circle :cx="flag.metrics.rightHoleX" cy="60" r="1.8"/>
                    <circle :cx="flag.metrics.rightHoleX" cy="71" r="1.8"/>

                    <!-- Volutas decorativas superiores y corazoncito -->
                    <path :d="flag.metrics.leftVoluta" stroke="#FAF5EC" stroke-width="1.8" fill="none" stroke-linecap="round"/>
                    <path :d="flag.metrics.rightVoluta" stroke="#FAF5EC" stroke-width="1.8" fill="none" stroke-linecap="round"/>
                    <path d="M 0 21 C -2.5 17, -7 17, -7 21 C -7 25, 0 29, 0 29 C 0 29, 7 25, 7 21 C 7 17, 2.5 17, 0 21 Z" fill="#FAF5EC"/>

                    <!-- Festones florales troquelados en la base inferior -->
                    <!-- Festón Izquierdo -->
                    <ellipse :cx="flag.metrics.leftClusterCenter - 6" cy="98" rx="2" ry="5.5" :transform="`rotate(-20 ${flag.metrics.leftClusterCenter - 6} 98)`"/>
                    <ellipse :cx="flag.metrics.leftClusterCenter" cy="100" rx="2" ry="6"/>
                    <ellipse :cx="flag.metrics.leftClusterCenter + 6" cy="98" rx="2" ry="5.5" :transform="`rotate(20 ${flag.metrics.leftClusterCenter + 6} 98)`"/>

                    <!-- Festón Central -->
                    <ellipse cx="-6" cy="82" rx="1.8" ry="4.5" transform="rotate(-25 -6 82)"/>
                    <ellipse cx="0" cy="84" rx="1.8" ry="5"/>
                    <ellipse cx="6" cy="82" rx="1.8" ry="4.5" transform="rotate(25 6 82)"/>

                    <!-- Festón Derecho -->
                    <ellipse :cx="flag.metrics.rightClusterCenter - 6" cy="98" rx="2" ry="5.5" :transform="`rotate(-20 ${flag.metrics.rightClusterCenter - 6} 98)`"/>
                    <ellipse :cx="flag.metrics.rightClusterCenter" cy="100" rx="2" ry="6"/>
                    <ellipse :cx="flag.metrics.rightClusterCenter + 6" cy="98" rx="2" ry="5.5" :transform="`rotate(20 ${flag.metrics.rightClusterCenter + 6} 98)`"/>
                  </g>

                  <!-- Marco calado punteado tradicional artesanal alrededor del texto (sin mancha negra) -->
                  <rect 
                    :x="-flag.metrics.windowWidth / 2" 
                    y="33" 
                    :width="flag.metrics.windowWidth" 
                    height="36" 
                    rx="4" 
                    fill="none" 
                    stroke="#FAF5EC" 
                    stroke-width="1.2" 
                    stroke-dasharray="3 2"
                    opacity="0.85"
                  />

                  <!-- Texto de la Sección (Siempre grande, nítido, sin caja oscura) -->
                  <text 
                    x="0" 
                    y="51.5" 
                    text-anchor="middle" 
                    dominant-baseline="central" 
                    fill="#FFFFFF" 
                    stroke="#1E0E05"
                    stroke-width="1.2"
                    paint-order="stroke fill"
                    font-family="'Syne', 'Plus Jakarta Sans', sans-serif" 
                    :font-size="flag.metrics.fontSize" 
                    font-weight="900" 
                    :letter-spacing="flag.metrics.letterSpacing" 
                    :textLength="flag.metrics.textLengthClamp"
                    :lengthAdjust="flag.metrics.textLengthClamp ? 'spacingAndGlyphs' : undefined"
                    class="flag-label-text filter drop-shadow-[0_1.5px_2px_rgba(0,0,0,0.35)] pointer-events-none select-none"
                  >
                    {{ flag.label }}
                  </text>

                </g>

                <!-- 3. Brazo Frontal de las Pinzas (Acopladas a la posición dinámica) -->
                <!-- Pinza Frontal Izquierda -->
                <g :transform="`translate(${flag.metrics.leftPinX}, 0)`" class="pointer-events-none">
                  <ellipse cx="0" cy="14" rx="4" ry="1.5" fill="#3A1C0A" opacity="0.25" />
                  <rect x="-3" y="-11" width="6" height="24" rx="1" fill="url(#pinWoodFront)" stroke="#3E1F08" stroke-width="0.7" />
                  <line x1="-1" y1="-10" x2="-1" y2="12" stroke="#7A3E12" stroke-width="0.5" stroke-dasharray="2 1" opacity="0.6" />
                  <rect x="-4.5" y="-2" width="9" height="3.5" rx="1.2" fill="url(#pinSpring)" stroke="#27272A" stroke-width="0.6" />
                  <line x1="-3" y1="-0.2" x2="3" y2="-0.2" stroke="#FFFFFF" stroke-width="0.6" stroke-linecap="round" />
                </g>

                <!-- Pinza Frontal Derecha -->
                <g :transform="`translate(${flag.metrics.rightPinX}, 0)`" class="pointer-events-none">
                  <ellipse cx="0" cy="14" rx="4" ry="1.5" fill="#3A1C0A" opacity="0.25" />
                  <rect x="-3" y="-11" width="6" height="24" rx="1" fill="url(#pinWoodFront)" stroke="#3E1F08" stroke-width="0.7" />
                  <line x1="-1" y1="-10" x2="-1" y2="12" stroke="#7A3E12" stroke-width="0.5" stroke-dasharray="2 1" opacity="0.6" />
                  <rect x="-4.5" y="-2" width="9" height="3.5" rx="1.2" fill="url(#pinSpring)" stroke="#27272A" stroke-width="0.6" />
                  <line x1="-3" y1="-0.2" x2="3" y2="-0.2" stroke="#FFFFFF" stroke-width="0.6" stroke-linecap="round" />
                </g>

              </g>
            </a>
          </template>
        </g>

        <!-- ==================== CAPA 3: LA CUERDA CONTINUA DE YUTE RÚSTICA ==================== -->
        <g id="rope-layer" class="pointer-events-none">
          <!-- Sombra de la cuerda suave sobre el pergamino -->
          <path 
            d="M 35 25 C 420 85, 1020 85, 1405 25" 
            stroke="#3A1C0A" 
            stroke-width="3.2" 
            stroke-linecap="round" 
            opacity="0.25" 
            fill="none"
            transform="translate(0, 3)"
          />
          <!-- Cuerda Principal que recorre todas las pinzas -->
          <path 
            d="M 35 24 C 420 84, 1020 84, 1405 24" 
            stroke="url(#ropeGrad)" 
            stroke-width="3.2" 
            stroke-linecap="round" 
            fill="none"
          />
          <!-- Torsión y brillo del hilo de yute -->
          <path 
            d="M 35 23.5 C 420 83.5, 1020 83.5, 1405 23.5" 
            stroke="#FDEBD0" 
            stroke-width="1.0" 
            stroke-dasharray="4 3" 
            stroke-linecap="round" 
            opacity="0.75" 
            fill="none"
          />
        </g>

        <!-- ==================== CAPA 4: CENTRO - LOGO REAL DEL USUARIO (logo.png) ==================== -->
        <a 
          href="#" 
          class="pointer-events-auto cursor-pointer group" 
          title="El Chili Negro - Ir al inicio"
        >
          <g id="center-logo-badge" class="logo-papel-container">
            <!-- Halo cálido sutil de ambiente -->
            <ellipse 
              cx="720" 
              cy="125" 
              rx="95" 
              ry="50" 
              fill="url(#logoGlow)" 
              opacity="0.20" 
              class="group-hover:opacity-40 transition-opacity duration-300 pointer-events-none" 
            />

            <!-- Imagen del Logo Oficial en Papel Picado de El Chili Negro (logo.png recortado limpio sin manchas) -->
            <image 
              :href="logoImg" 
              x="625" 
              y="40" 
              width="190" 
              height="190" 
              preserveAspectRatio="xMidYMid meet"
              clip-path="url(#logoCleanClip)"
              class="logo-image"
            />

            <!-- Amarre de cuerda izquierda con la soga principal -->
            <g id="cord-tie-left" class="pointer-events-none">
              <ellipse cx="625" cy="68.5" rx="3.5" ry="6" fill="#FACC15" stroke="#854D0E" stroke-width="0.9" transform="rotate(-15 625 68.5)" />
              <path d="M 623 64 Q 625 62 627 65" stroke="#FEF08A" stroke-width="1.2" fill="none" />
              <path d="M 624 72 Q 622 76 623 80" stroke="#EAB308" stroke-width="2.2" stroke-linecap="round" fill="none" />
            </g>

            <!-- Amarre de cuerda derecha con la soga principal -->
            <g id="cord-tie-right" class="pointer-events-none">
              <ellipse cx="815" cy="68.5" rx="3.5" ry="6" fill="#FACC15" stroke="#854D0E" stroke-width="0.9" transform="rotate(15 815 68.5)" />
              <path d="M 813 64 Q 815 62 817 65" stroke="#FEF08A" stroke-width="1.2" fill="none" />
              <path d="M 816 72 Q 818 76 817 80" stroke="#EAB308" stroke-width="2.2" stroke-linecap="round" fill="none" />
            </g>
          </g>
        </a>

        <!-- ==================== CAPA 5: EXTREMOS - CLAVOS Y NUDOS MARINEROS ==================== -->
        <!-- Extremo Izquierdo: Clavo & Nudo en (35, 24) -->
        <g id="anchor-knot-left" class="pointer-events-none">
          <ellipse cx="35" cy="27" rx="8" ry="4" fill="#3A1C0A" opacity="0.25" />
          <circle cx="34" cy="24" r="5.5" fill="url(#nailGrad)" stroke="#111111" stroke-width="1.2"/>
          <circle cx="32.5" cy="22.5" r="1.5" fill="#FFFFFF" opacity="0.7"/>
          <ellipse cx="35" cy="24" rx="10" ry="7" stroke="url(#ropeGrad)" stroke-width="3" fill="none" transform="rotate(-15 35 24)"/>
          <ellipse cx="36" cy="25" rx="6" ry="4.5" fill="url(#ropeGrad)" stroke="#4A260D" stroke-width="1.2"/>
          <path d="M 31 23 Q 35 28 39 24" stroke="#FDEBD0" stroke-width="1.2" fill="none"/>
          <path d="M 34 29 Q 31 40 33 48" stroke="url(#ropeGrad)" stroke-width="2.8" stroke-linecap="round" fill="none"/>
          <path d="M 32 46 L 30 52" stroke="#C28D58" stroke-width="1.2" stroke-linecap="round"/>
          <path d="M 33 47 L 33 53" stroke="#FDEBD0" stroke-width="1.4" stroke-linecap="round"/>
        </g>

        <!-- Extremo Derecho: Clavo & Nudo en (1405, 24) -->
        <g id="anchor-knot-right" class="pointer-events-none">
          <ellipse cx="1405" cy="27" rx="8" ry="4" fill="#3A1C0A" opacity="0.25" />
          <circle cx="1406" cy="24" r="5.5" fill="url(#nailGrad)" stroke="#111111" stroke-width="1.2"/>
          <circle cx="1404.5" cy="22.5" r="1.5" fill="#FFFFFF" opacity="0.7"/>
          <ellipse cx="1405" cy="24" rx="10" ry="7" stroke="url(#ropeGrad)" stroke-width="3" fill="none" transform="rotate(15 1405 24)"/>
          <ellipse cx="1404" cy="25" rx="6" ry="4.5" fill="url(#ropeGrad)" stroke="#4A260D" stroke-width="1.2"/>
          <path d="M 1401 24 Q 1405 28 1409 23" stroke="#FDEBD0" stroke-width="1.2" fill="none"/>
          <path d="M 1406 29 Q 1409 40 1407 48" stroke="url(#ropeGrad)" stroke-width="2.8" stroke-linecap="round" fill="none"/>
          <path d="M 1406 46 L 1404 52" stroke="#784218" stroke-width="1.2" stroke-linecap="round"/>
          <path d="M 1407 47 L 1407 53" stroke="#FDEBD0" stroke-width="1.4" stroke-linecap="round"/>
        </g>

      </svg>

    </div>

  </header>
</template>

<style scoped>
.flag-link-item {
  cursor: pointer;
  text-decoration: none;
}

/* El punto de giro es exactamente Y = 4px (bajo las pinzas) */
.paper-seamless-sheet {
  transform-origin: 0px 4px;
  transition: transform 0.4s cubic-bezier(0.22, 1, 0.36, 1), filter 0.3s ease;
}

/* Al pasar el mouse: La parte superior se queda fija en las pinzas y la parte baja se balancea suavemente como papel real */
.flag-link-item:hover .paper-seamless-sheet {
  animation: naturalPaperPendulum 1.5s ease-in-out infinite alternate;
  filter: drop-shadow(0 12px 20px rgba(0, 0, 0, 0.95)) brightness(1.1);
}

/* Efecto pendular orgánico: El tope se mueve 0px y la parte de abajo oscila suavemente */
@keyframes naturalPaperPendulum {
  0% {
    transform: rotate(0deg);
  }
  30% {
    transform: rotate(3.2deg) skewX(1deg);
  }
  70% {
    transform: rotate(-2.8deg) skewX(-1deg);
  }
  100% {
    transform: rotate(2deg) skewX(0.5deg);
  }
}

.flag-link-item:hover .flag-label-text {
  filter: drop-shadow(0 0 8px rgba(255, 255, 255, 0.95));
}

.logo-papel-container {
  transform-origin: 720px 68px;
  transition: transform 0.4s cubic-bezier(0.22, 1, 0.36, 1), filter 0.3s ease;
}

.group:hover .logo-papel-container {
  animation: naturalPaperPendulum 1.5s ease-in-out infinite alternate;
  filter: drop-shadow(0 12px 20px rgba(0, 0, 0, 0.95)) brightness(1.1);
}
</style>