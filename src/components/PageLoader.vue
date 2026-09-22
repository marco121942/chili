<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from "vue";
import load1Img from "@/assets/img/load-1.png";
import load2Img from "@/assets/img/load-2.png";

const props = withDefaults(
  defineProps<{
    customTask?: () => Promise<void>;
  }>(),
  {}
);

const emit = defineEmits<{
  (e: "loaded"): void;
}>();

const progress = ref(0);
const isVisible = ref(true);
const isExiting = ref(false);
const phase = ref(0);

// Chispas y brasas que flotan impulsadas por el calor del fuego
interface Ember {
  id: number;
  x: number;
  y: number;
  r: number;
  speedY: number;
  speedX: number;
  swayFreq: number;
  phaseOffset: number;
  color: string;
  opacity: number;
}

const embers = ref<Ember[]>([
  { id: 1, x: 512, y: 920, r: 4.0, speedY: 4.2, speedX: 0.6, swayFreq: 2.5, phaseOffset: 0.2, color: "#FFE500", opacity: 0.9 },
  { id: 2, x: 440, y: 950, r: 2.8, speedY: 3.5, speedX: -0.8, swayFreq: 3.1, phaseOffset: 1.5, color: "#FF8C00", opacity: 0.8 },
  { id: 3, x: 580, y: 900, r: 3.5, speedY: 4.8, speedX: 0.7, swayFreq: 2.1, phaseOffset: 2.8, color: "#FF3B30", opacity: 0.75 },
  { id: 4, x: 380, y: 880, r: 2.5, speedY: 3.2, speedX: -0.5, swayFreq: 3.8, phaseOffset: 3.4, color: "#FFE500", opacity: 0.85 },
  { id: 5, x: 640, y: 930, r: 4.5, speedY: 5.0, speedX: 0.9, swayFreq: 2.9, phaseOffset: 4.1, color: "#FF8C00", opacity: 0.95 },
  { id: 6, x: 490, y: 870, r: 2.2, speedY: 3.8, speedX: 0.3, swayFreq: 4.2, phaseOffset: 5.0, color: "#FFFFFF", opacity: 0.9 },
  { id: 7, x: 550, y: 960, r: 3.2, speedY: 4.4, speedX: -0.6, swayFreq: 3.0, phaseOffset: 1.1, color: "#FFE500", opacity: 0.8 },
  { id: 8, x: 420, y: 910, r: 3.8, speedY: 3.9, speedX: 0.8, swayFreq: 2.4, phaseOffset: 2.0, color: "#FF5500", opacity: 0.7 },
  { id: 9, x: 600, y: 890, r: 2.6, speedY: 4.6, speedX: -0.7, swayFreq: 3.5, phaseOffset: 3.7, color: "#FFE500", opacity: 0.85 },
  { id: 10, x: 470, y: 940, r: 4.2, speedY: 5.2, speedX: 0.5, swayFreq: 2.7, phaseOffset: 4.8, color: "#FF8C00", opacity: 0.9 },
  { id: 11, x: 530, y: 850, r: 2.0, speedY: 3.4, speedX: -0.4, swayFreq: 4.0, phaseOffset: 0.9, color: "#FFFFFF", opacity: 0.95 },
  { id: 12, x: 360, y: 920, r: 3.0, speedY: 4.1, speedX: -0.9, swayFreq: 3.3, phaseOffset: 1.8, color: "#FF3B30", opacity: 0.7 },
  { id: 13, x: 660, y: 950, r: 3.4, speedY: 4.5, speedX: 0.8, swayFreq: 2.8, phaseOffset: 2.5, color: "#FF8C00", opacity: 0.8 },
  { id: 14, x: 510, y: 980, r: 2.4, speedY: 3.6, speedX: 0.2, swayFreq: 3.6, phaseOffset: 3.9, color: "#FFE500", opacity: 0.85 },
  { id: 15, x: 450, y: 860, r: 3.6, speedY: 4.7, speedX: -0.5, swayFreq: 2.6, phaseOffset: 4.5, color: "#FF5500", opacity: 0.8 },
]);

// Posición vertical del frente de fuego (de 1040 en 0% a -30 en 100%)
const fireY = computed(() => {
  return 1040 - (progress.value / 100) * 1070;
});

// Altura de las lenguas de fuego: activa durante el encendido, suave al inicio/fin
const flameMaxHeight = computed(() => {
  if (progress.value <= 0 || progress.value >= 100) return 0;
  // Curva de fuego viva que alcanza hasta 75px en su punto máximo
  return Math.sin((progress.value / 100) * Math.PI) * 75;
});

// Generador de lenguas de fuego en llamas (puntas afiladas hacia arriba)
const generateFlameCurve = (heightMultiplier: number, phaseOffset: number) => {
  const yBase = fireY.value;
  const maxH = flameMaxHeight.value * heightMultiplier;
  const p = phase.value + phaseOffset;

  const numTongues = 16;
  const tongueW = 1024 / numTongues;
  
  // Guardamos las lenguas de fuego
  const tongues: { xStart: number; yStart: number; xTip: number; yTip: number; xEnd: number; yEnd: number }[] = [];

  for (let i = 0; i < numTongues; i++) {
    const xStart = i * tongueW;
    const xEnd = (i + 1) * tongueW;
    
    // Variación orgánica de cada llama
    const flicker1 = Math.sin(p * 5.2 + i * 2.3);
    const flicker2 = Math.cos(p * 3.7 + i * 4.1);
    const h = Math.max(8, maxH * (0.65 + 0.35 * flicker1));
    
    // Vadeo horizontal de la punta de la llama
    const sway = Math.sin(p * 4.0 + i * 1.7) * (tongueW * 0.32);
    const xTip = xStart + tongueW * 0.5 + sway;
    const yTip = yBase - h;

    // Valle de la llama con pequeñas variaciones orgánicas
    const yValleyStart = yBase + flicker2 * 8;
    const yValleyEnd = yBase + Math.sin(p * 3.1 + (i + 1) * 2.3) * 8;

    tongues.push({
      xStart,
      yStart: yValleyStart,
      xTip,
      yTip,
      xEnd,
      yEnd: yValleyEnd
    });
  }

  // Trazar el camino SVG con curvas bézier afiladas en las puntas
  let pathD = "";
  for (let i = 0; i < tongues.length; i++) {
    const t = tongues[i];
    if (i === 0) {
      pathD += `M ${t.xStart.toFixed(1)} ${t.yStart.toFixed(1)} `;
    }

    // Subida hacia la punta de la llama
    const cx1 = t.xStart + tongueW * 0.22;
    const cy1 = t.yStart - (t.yStart - t.yTip) * 0.4;
    const cx2 = t.xTip - 5;
    const cy2 = t.yTip + 14;

    // Bajada hacia el siguiente valle
    const cx3 = t.xTip + 5;
    const cy3 = t.yTip + 14;
    const cx4 = t.xEnd - tongueW * 0.22;
    const cy4 = t.yEnd - (t.yEnd - t.yTip) * 0.4;

    pathD += `C ${cx1.toFixed(1)} ${cy1.toFixed(1)}, ${cx2.toFixed(1)} ${cy2.toFixed(1)}, ${t.xTip.toFixed(1)} ${t.yTip.toFixed(1)} `;
    pathD += `C ${cx3.toFixed(1)} ${cy3.toFixed(1)}, ${cx4.toFixed(1)} ${cy4.toFixed(1)}, ${t.xEnd.toFixed(1)} ${t.yEnd.toFixed(1)} `;
  }

  return { pathD, tongues };
};

// ClipPath principal: Todo lo que esté debajo del frente de fuego se vuelve a color
const fireClipPathD = computed(() => {
  const { pathD } = generateFlameCurve(1.0, 0);
  return `${pathD} L 1024 1060 L 0 1060 Z`;
});

// Cinta de fuego viva (SÓLO en la frontera de combustión, sin tapar la calavera de abajo)
const flameRibbonPathD = computed(() => {
  const { pathD } = generateFlameCurve(1.15, 0.35);
  const yBottom = Math.min(1050, fireY.value + 35);
  return `${pathD} L 1024 ${yBottom.toFixed(1)} L 0 ${yBottom.toFixed(1)} Z`;
});

// Núcleo caliente de la llama (banda estrecha dorada)
const innerFlameRibbonPathD = computed(() => {
  const { pathD } = generateFlameCurve(0.75, 1.8);
  const yBottom = Math.min(1050, fireY.value + 20);
  return `${pathD} L 1024 ${yBottom.toFixed(1)} L 0 ${yBottom.toFixed(1)} Z`;
});

// Línea de cresta viva de las llamas
const flameRimLineD = computed(() => {
  const { pathD } = generateFlameCurve(1.0, 0);
  return pathD;
});

// ==================== PÉTALOS DE CEMPASÚCHIL (ESTILO PUENTE DE COCO) ====================
const cempasuchilCanvasRef = ref<HTMLCanvasElement | null>(null);

interface CempasuchilPetal {
  x: number;
  y: number;
  width: number;
  height: number;
  speedY: number;
  speedX: number;
  swayAmp: number;
  swayFreq: number;
  swayOffset: number;
  rotZ: number;
  rotSpeedZ: number;
  rotX: number;
  rotSpeedX: number;
  rotY: number;
  rotSpeedY: number;
  opacity: number;
  glow: boolean;
}

interface GoldenSparkle {
  x: number;
  y: number;
  r: number;
  speedY: number;
  speedX: number;
  pulse: number;
  pulseSpeed: number;
  color: string;
  opacity: number;
}

let cempasuchilFrameId: number | null = null;
let petals: CempasuchilPetal[] = [];
let sparkles: GoldenSparkle[] = [];

const initCempasuchil = (width: number, height: number) => {
  // Crear 55 pétalos variados con profundidad de campo
  petals = [];
  const petalCount = 55;
  for (let i = 0; i < petalCount; i++) {
    const scale = 0.65 + Math.random() * 0.7; // Profundidad (lejanos y cercanos)
    petals.push({
      x: Math.random() * width,
      y: Math.random() * height,
      width: (12 + Math.random() * 8) * scale,
      height: (22 + Math.random() * 14) * scale,
      speedY: (0.7 + Math.random() * 1.5) * scale,
      speedX: (Math.random() * 0.8 - 0.2) * scale,
      swayAmp: 25 + Math.random() * 35,
      swayFreq: 0.015 + Math.random() * 0.02,
      swayOffset: Math.random() * Math.PI * 2,
      rotZ: Math.random() * Math.PI * 2,
      rotSpeedZ: (Math.random() - 0.5) * 0.03,
      rotX: Math.random() * Math.PI * 2,
      rotSpeedX: 0.015 + Math.random() * 0.03,
      rotY: Math.random() * Math.PI * 2,
      rotSpeedY: 0.02 + Math.random() * 0.04,
      opacity: 0.5 + Math.random() * 0.45,
      glow: Math.random() > 0.4
    });
  }

  // 35 partículas de polen dorado / chispas místicas flotantes (como en Coco)
  sparkles = [];
  const sparkleCount = 35;
  for (let i = 0; i < sparkleCount; i++) {
    sparkles.push({
      x: Math.random() * width,
      y: Math.random() * height,
      r: 1.5 + Math.random() * 2.2,
      speedY: 0.3 + Math.random() * 0.9,
      speedX: (Math.random() - 0.5) * 0.6,
      pulse: Math.random() * Math.PI * 2,
      pulseSpeed: 0.03 + Math.random() * 0.04,
      color: Math.random() > 0.5 ? "#D97706" : "#EA580C",
      opacity: 0.4 + Math.random() * 0.5
    });
  }
};

const drawCempasuchilPetal = (ctx: CanvasRenderingContext2D, p: CempasuchilPetal) => {
  ctx.save();
  ctx.translate(p.x, p.y);
  ctx.rotate(p.rotZ);

  // Volteo tridimensional en el aire
  const scaleX = Math.cos(p.rotY);
  const scaleY = Math.cos(p.rotX);
  ctx.scale(scaleX, scaleY);

  const w = p.width;
  const h = p.height;

  // Gradiente característico de flor de cempasúchil
  const grad = ctx.createLinearGradient(0, h * 0.45, 0, -h * 0.5);
  grad.addColorStop(0, "#9A2600");   // Base naranja rojizo
  grad.addColorStop(0.3, "#E65100");  // Naranja fuego
  grad.addColorStop(0.7, "#FFA000");  // Naranja cempasúchil radiante
  grad.addColorStop(1, "#FFD54F");    // Borde amarillo brillante

  ctx.fillStyle = grad;
  ctx.globalAlpha = p.opacity;

  if (p.glow) {
    ctx.shadowColor = "rgba(230, 81, 0, 0.4)";
    ctx.shadowBlur = 6;
  }

  // Silueta festoneada del pétalo de Cempasúchil
  ctx.beginPath();
  ctx.moveTo(0, h * 0.45);
  ctx.bezierCurveTo(-w * 0.45, h * 0.2, -w * 0.85, -h * 0.15, -w * 0.65, -h * 0.42);
  ctx.bezierCurveTo(-w * 0.35, -h * 0.55, -w * 0.1, -h * 0.45, 0, -h * 0.52);
  ctx.bezierCurveTo(w * 0.1, -h * 0.45, w * 0.35, -h * 0.55, w * 0.65, -h * 0.42);
  ctx.bezierCurveTo(w * 0.85, -h * 0.15, w * 0.45, h * 0.2, 0, h * 0.45);
  ctx.closePath();
  ctx.fill();

  // Nervadura central sutil
  ctx.strokeStyle = "rgba(154, 38, 0, 0.35)";
  ctx.lineWidth = 0.8;
  ctx.beginPath();
  ctx.moveTo(0, h * 0.4);
  ctx.quadraticCurveTo(0, 0, 0, -h * 0.35);
  ctx.stroke();

  ctx.restore();
};

const drawSparkle = (ctx: CanvasRenderingContext2D, s: GoldenSparkle) => {
  ctx.save();
  ctx.translate(s.x, s.y);
  ctx.fillStyle = s.color;
  const currentOpacity = s.opacity * (0.6 + 0.4 * Math.sin(s.pulse));
  ctx.globalAlpha = Math.max(0.1, Math.min(1, currentOpacity));
  ctx.shadowColor = "#D97706";
  ctx.shadowBlur = 6;
  ctx.beginPath();
  ctx.arc(0, 0, s.r, 0, Math.PI * 2);
  ctx.fill();
  ctx.restore();
};

let animationFrameId: number | null = null;

onMounted(() => {
  // Inicialización del canvas de pétalos
  const canvas = cempasuchilCanvasRef.value;
  if (canvas) {
    const ctx = canvas.getContext("2d");
    const handleResize = () => {
      if (!canvas) return;
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
      initCempasuchil(canvas.width, canvas.height);
    };

    handleResize();
    window.addEventListener("resize", handleResize);

    let time = 0;
    const renderCempasuchil = () => {
      if (!ctx || !canvas) return;
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      time += 1;

      // Dibujar partículas de polen dorado flotantes
      sparkles.forEach((s) => {
        s.y -= s.speedY;
        s.x += s.speedX + Math.sin(time * 0.02) * 0.3;
        s.pulse += s.pulseSpeed;
        if (s.y < -10) {
          s.y = canvas.height + 10;
          s.x = Math.random() * canvas.width;
        }
        drawSparkle(ctx, s);
      });

      // Dibujar pétalos de Cempasúchil flotantes
      petals.forEach((p) => {
        p.y += p.speedY;
        p.x += p.speedX + Math.sin(time * p.swayFreq + p.swayOffset) * (p.swayAmp * 0.025);
        p.rotZ += p.rotSpeedZ;
        p.rotX += p.rotSpeedX;
        p.rotY += p.rotSpeedY;

        // Si el pétalo sale por abajo o costados, reaparece suavemente por arriba
        if (p.y > canvas.height + 40) {
          p.y = -40;
          p.x = Math.random() * canvas.width;
        }
        if (p.x > canvas.width + 40) p.x = -40;
        if (p.x < -40) p.x = canvas.width + 40;

        drawCempasuchilPetal(ctx, p);
      });

      cempasuchilFrameId = requestAnimationFrame(renderCempasuchil);
    };

    renderCempasuchil();
  }

  const urlParams = typeof window !== "undefined" ? new URLSearchParams(window.location.search) : null;
  const previewProgress = urlParams?.get("progress");

  if (urlParams?.get("skip") === "true") {
    progress.value = 100;
    isVisible.value = false;
    emit("loaded");
    return;
  }

  if (previewProgress !== null && previewProgress !== undefined && !isNaN(Number(previewProgress))) {
    // Modo inspección/preview fijo para visualizar las llamas en un porcentaje exacto
    progress.value = Math.max(0, Math.min(100, Number(previewProgress)));

    const tickPreview = () => {
      phase.value += 0.085;
      const currentFireY = fireY.value;
      embers.value.forEach((ember) => {
        ember.y -= ember.speedY;
        ember.x += Math.sin(phase.value * ember.swayFreq + ember.phaseOffset) * ember.speedX * 2.2;
        if (ember.y < currentFireY - 180 || ember.y < 20) {
          ember.y = Math.min(1000, currentFireY + 40 + Math.random() * 80);
          ember.x = 320 + Math.random() * 384;
        }
      });
      animationFrameId = requestAnimationFrame(tickPreview);
    };
    animationFrameId = requestAnimationFrame(tickPreview);
    return;
  }

  // Rastreo del estado REAL de carga de recursos del navegador
  const isRealLoadComplete = ref(false);
  const realProgress = ref(0);

  const initResourceTracker = async () => {
    let loadedWeight = 0;
    const hasCustomTask = Boolean(props.customTask);
    const totalWeight = hasCustomTask ? 120 : 100;

    const addProgress = (weight: number) => {
      loadedWeight += weight;
      realProgress.value = Math.min(100, Math.round((loadedWeight / totalWeight) * 100));
    };

    const tasks: Promise<unknown>[] = [];

    // Tarea 1: Pre-carga y decodificación GPU de load-1.png (1.05MB)
    tasks.push(
      new Promise<void>((resolve) => {
        const img = new Image();
        img.src = load1Img;
        const onDone = () => {
          if ("decode" in img) {
            img.decode().catch(() => {}).then(() => {
              addProgress(35);
              resolve();
            });
          } else {
            addProgress(35);
            resolve();
          }
        };
        if (img.complete) {
          onDone();
        } else {
          img.onload = onDone;
          img.onerror = () => {
            addProgress(35);
            resolve();
          };
        }
      })
    );

    // Tarea 2: Pre-carga y decodificación GPU de load-2.png (1.07MB)
    tasks.push(
      new Promise<void>((resolve) => {
        const img = new Image();
        img.src = load2Img;
        const onDone = () => {
          if ("decode" in img) {
            img.decode().catch(() => {}).then(() => {
              addProgress(35);
              resolve();
            });
          } else {
            addProgress(35);
            resolve();
          }
        };
        if (img.complete) {
          onDone();
        } else {
          img.onload = onDone;
          img.onerror = () => {
            addProgress(35);
            resolve();
          };
        }
      })
    );

    // Tarea 3: Fuentes tipográficas (Syne & Plus Jakarta Sans)
    tasks.push(
      (async () => {
        try {
          if (typeof document !== "undefined" && "fonts" in document) {
            await document.fonts.ready;
          }
        } catch {
          // Fallback en entornos donde document.fonts no esté disponible
        }
        addProgress(15);
      })()
    );

    // Tarea 4: Estado completo del DOM / Window
    tasks.push(
      new Promise<void>((resolve) => {
        if (typeof document !== "undefined" && document.readyState === "complete") {
          addProgress(15);
          resolve();
        } else if (typeof window !== "undefined") {
          const onWinLoad = () => {
            window.removeEventListener("load", onWinLoad);
            addProgress(15);
            resolve();
          };
          window.addEventListener("load", onWinLoad);
          setTimeout(() => {
            window.removeEventListener("load", onWinLoad);
            addProgress(15);
            resolve();
          }, 3500);
        } else {
          addProgress(15);
          resolve();
        }
      })
    );

    // Tarea 5: Tarea asíncrona personalizada (si se pasa por props, ej. fetch de menú)
    if (props.customTask) {
      tasks.push(
        (async () => {
          try {
            await props.customTask!();
          } catch (err) {
            console.warn("Custom task failed in loader", err);
          }
          addProgress(20);
        })()
      );
    }

    // Esperar a que todos los recursos reales se hayan resuelto y decodificado
    await Promise.allSettled(tasks);
    realProgress.value = 100;
    isRealLoadComplete.value = true;
  };

  initResourceTracker();

  let displayedProgress = 0;
  let lastTimestamp = performance.now();
  let isFinished = false;

  const tick = (now: number) => {
    const dt = Math.min(0.1, (now - lastTimestamp) / 1000); // delta-time en segundos
    lastTimestamp = now;

    // Techo dinámico: Si los recursos aún están cargando, el contador se adapta al progreso real
    // y se detiene en máximo 88% esperando la confirmación final de la red.
    const targetCeiling = isRealLoadComplete.value ? 100 : Math.min(realProgress.value, 88);

    if (displayedProgress < targetCeiling) {
      // Interpolación suave y orgánica (acelera y suaviza naturalmente)
      const diff = targetCeiling - displayedProgress;
      const step = isRealLoadComplete.value
        ? Math.max(0.7, diff * Math.min(1, dt * 5.5))
        : Math.max(0.2, diff * Math.min(1, dt * 3.5));
      displayedProgress = Math.min(targetCeiling, displayedProgress + step);
    }

    const currentIntProgress = Math.min(100, Math.floor(displayedProgress));
    progress.value = currentIntProgress;

    // Movimiento rítmico continuo de las llamas
    phase.value += 0.085;

    // Actualización de chispas y brasas incandescentes
    const currentFireY = fireY.value;
    embers.value.forEach((ember) => {
      ember.y -= ember.speedY;
      ember.x += Math.sin(phase.value * ember.swayFreq + ember.phaseOffset) * ember.speedX * 2.2;

      // Si la chispa sube mucho más allá del fuego o sale de la vista, renace en la brasa
      if (ember.y < currentFireY - 180 || ember.y < 20) {
        ember.y = Math.min(1000, currentFireY + 40 + Math.random() * 80);
        ember.x = 320 + Math.random() * 384;
      }
    });

    if (displayedProgress >= 100 && isRealLoadComplete.value) {
      if (!isFinished) {
        isFinished = true;
        progress.value = 100;

        // Pausa serena de 300ms al 100% para apreciar la calavera en todo su esplendor
        setTimeout(() => {
          // Fundido suave y limpio (Opción 1 limpia)
          isExiting.value = true;

          // Tras 700ms de desvanecimiento sedoso, ocultar y emitir evento loaded
          setTimeout(() => {
            isVisible.value = false;
            emit("loaded");
          }, 700);
        }, 300);
      }
    } else {
      animationFrameId = requestAnimationFrame(tick);
    }
  };

  animationFrameId = requestAnimationFrame(tick);
});

onUnmounted(() => {
  if (animationFrameId !== null) {
    cancelAnimationFrame(animationFrameId);
  }
  if (cempasuchilFrameId !== null) {
    cancelAnimationFrame(cempasuchilFrameId);
  }
});
</script>

<template>
  <div
    v-if="isVisible"
    :class="[
      'fixed inset-0 z-[100] flex flex-col items-center justify-center select-none overflow-hidden transition-opacity duration-700 ease-in-out',
      isExiting ? 'opacity-0 pointer-events-none' : 'opacity-100'
    ]"
    style="background: radial-gradient(circle at 50% 45%, #FFFFFF 0%, #F9F6F0 52%, #EDE5D8 100%);"
  >
    <!-- LIENZO DE PÉTALOS DE CEMPASÚCHIL Y POLEN DORADO (ESTILO COCO) -->
    <canvas
      ref="cempasuchilCanvasRef"
      class="absolute inset-0 w-full h-full pointer-events-none z-0"
    ></canvas>

    <!-- Atmósfera Solar / Luz Natural de Hacienda Mexicana -->
    <div class="absolute inset-0 overflow-hidden pointer-events-none z-[1]">
      <!-- Resplandor solar ámbar cálido en el centro -->
      <div
        class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[700px] h-[700px] bg-[#FF9A00] opacity-12 blur-[160px] rounded-full"
      ></div>

      <!-- Sutil toque de calor de agave cocido -->
      <div
        class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[500px] h-[500px] bg-[#E64A19] opacity-8 blur-[140px] rounded-full"
      ></div>

      <!-- Viñeta suave de pergamino en las esquinas -->
      <div
        class="absolute inset-0 bg-gradient-to-t from-[#E2D8C7]/50 via-transparent to-[#E2D8C7]/30 pointer-events-none"
      ></div>
    </div>

    <!-- Contenedor central del Loader de Fuego -->
    <div class="relative z-10 flex flex-col items-center">
      
      <!-- ==================== CALAVERA CON FUEGO DE ABAJO HACIA ARRIBA ==================== -->
      <div class="relative w-[320px] h-[320px] sm:w-[390px] sm:h-[390px] md:w-[450px] md:h-[450px] drop-shadow-[0_25px_45px_rgba(70,25,10,0.18)]">
        <svg
          viewBox="0 0 1024 1024"
          class="w-full h-full overflow-visible select-none"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <defs>
            <!-- Filtro para transformar cualquier pixel no transparente en blanco puro (máscara de silueta perfecta) -->
            <filter id="alphaSolidWhite">
              <feColorMatrix type="matrix" values="0 0 0 0 1   0 0 0 0 1   0 0 0 0 1   0 0 0 1 0" />
            </filter>

            <!-- ClipPath con lenguas de fuego en movimiento -->
            <clipPath id="fireTonguesClip">
              <path :d="fireClipPathD" />
            </clipPath>

            <!-- Máscara de silueta sólida sin agujeros en el texto negro ni en los ojos -->
            <mask id="skullMask">
              <image
                :href="load1Img"
                width="1024"
                height="1024"
                filter="url(#alphaSolidWhite)"
                preserveAspectRatio="xMidYMid meet"
              />
            </mask>

            <!-- Gradiente de incandescencia térmica (calienta el metal plateado al rojo vivo) -->
            <linearGradient id="thermalHeatGrad" x1="0%" y1="0%" x2="0%" y2="100%">
              <stop offset="0%" stop-color="#FF3B30" stop-opacity="0" />
              <stop offset="30%" stop-color="#FF5500" stop-opacity="0.45" />
              <stop offset="70%" stop-color="#FF8C00" stop-opacity="0.8" />
              <stop offset="100%" stop-color="#FFE600" stop-opacity="0.95" />
            </linearGradient>

            <!-- Gradiente de fuego ardiente para la cinta de llamas -->
            <linearGradient id="outerFlameGrad" x1="0%" y1="0%" x2="0%" y2="100%">
              <stop offset="0%" stop-color="#FF1E00" stop-opacity="0.9" />
              <stop offset="40%" stop-color="#FF6B00" stop-opacity="0.7" />
              <stop offset="100%" stop-color="#FFB800" stop-opacity="0" />
            </linearGradient>

            <!-- Gradiente de fuego incandescente para el núcleo de las llamas -->
            <linearGradient id="innerFlameGrad" x1="0%" y1="0%" x2="0%" y2="100%">
              <stop offset="0%" stop-color="#FFFFFF" stop-opacity="0.95" />
              <stop offset="35%" stop-color="#FFE600" stop-opacity="0.8" />
              <stop offset="100%" stop-color="#FF8C00" stop-opacity="0" />
            </linearGradient>

            <!-- Filtro de resplandor intenso para el fuego -->
            <filter id="fireGlow" x="-40%" y="-40%" width="180%" height="180%">
              <feDropShadow dx="0" dy="0" stdDeviation="4" flood-color="#FFE600" flood-opacity="1" />
              <feDropShadow dx="0" dy="0" stdDeviation="12" flood-color="#FF6B00" flood-opacity="0.8" />
              <feDropShadow dx="0" dy="0" stdDeviation="24" flood-color="#FF1E00" flood-opacity="0.5" />
            </filter>

            <!-- Filtro para chispas -->
            <filter id="emberGlow" x="-50%" y="-50%" width="200%" height="200%">
              <feDropShadow dx="0" dy="0" stdDeviation="3" flood-color="#FFE600" flood-opacity="1" />
              <feDropShadow dx="0" dy="0" stdDeviation="8" flood-color="#FF5500" flood-opacity="0.8" />
            </filter>
          </defs>

          <!-- CAPA 1: CALAVERA BASE GRIS METÁLICA (load-1.png) -->
          <image
            :href="load1Img"
            width="1024"
            height="1024"
            preserveAspectRatio="xMidYMid meet"
          />

          <!-- CAPA 2: CALAVERA A TODO COLOR (load-2.png) 100% NÍTIDA Y SIN VELOS -->
          <g clip-path="url(#fireTonguesClip)">
            <image
              :href="load2Img"
              width="1024"
              height="1024"
              preserveAspectRatio="xMidYMid meet"
            />
          </g>

          <!-- CAPA 3: CINTA DE FUEGO LOCALIZADA EN LA FRONTERA DE COMBUSTIÓN -->
          <g v-if="progress > 0 && progress < 99" mask="url(#skullMask)" class="pointer-events-none">
            <!-- Cinta exterior de fuego rojo/naranja -->
            <path
              :d="flameRibbonPathD"
              fill="url(#outerFlameGrad)"
              style="mix-blend-mode: screen;"
            />

            <!-- Cinta interior de fuego amarillo ardiente -->
            <path
              :d="innerFlameRibbonPathD"
              fill="url(#innerFlameGrad)"
              style="mix-blend-mode: screen;"
            />

            <!-- Filamento de combustión viva blanco-dorado en la cresta de la llama -->
            <path
              :d="flameRimLineD"
              stroke="#FFE600"
              stroke-width="5"
              stroke-linecap="round"
              stroke-linejoin="round"
              fill="none"
              filter="url(#fireGlow)"
              opacity="0.95"
            />
            <path
              :d="flameRimLineD"
              stroke="#FFFFFF"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
              fill="none"
              opacity="1"
            />
          </g>


          <!-- CAPA 4: CHISPAS Y BRASAS INCANDESCENTES EN EL AIRE -->
          <g v-if="progress > 3 && progress < 98" class="pointer-events-none">
            <circle
              v-for="e in embers"
              :key="e.id"
              :cx="e.x"
              :cy="e.y"
              :r="e.r"
              :fill="e.color"
              :fill-opacity="e.opacity"
              filter="url(#emberGlow)"
            />
          </g>

        </svg>
      </div>

      <!-- Solo el número del contador -->
      <div class="mt-4 select-none font-['Syne']">
        <span class="font-black text-3xl sm:text-4xl text-[#1F0D05] tracking-tight tabular-nums">
          {{ progress }}<span class="text-lg sm:text-xl text-[#D97706] ml-0.5 font-bold">%</span>
        </span>
      </div>

    </div>
  </div>
</template>

<style scoped>
svg {
  transform: translateZ(0);
}
</style>
