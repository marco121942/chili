<script setup lang="ts">
import { ref, computed, onMounted } from "vue";
import { Flame, Heart, ArrowUpRight } from "lucide-vue-next";

export interface MenuItem {
  id: string;
  name: string;
  category: "tacos" | "micheladas" | "antojitos" | "cocteles";
  categoryLabel: string;
  tagline: string;
  description: string;
  price: number;
  originalPrice: number;
  discountPercent: number;
  image: string;
  spiciness: 0 | 1 | 2 | 3;
  highlight?: string;
  monumental?: boolean;
}

const activeCategory = ref<string>("todos");
const favorites = ref<Set<string>>(new Set(["mamalona-1l", "quesabirrias"]));

const toggleFavorite = (id: string) => {
  if (favorites.value.has(id)) {
    favorites.value.delete(id);
  } else {
    favorites.value.add(id);
  }
};

const categories = [
  { id: "todos", label: "Todos los Platillos" },
  { id: "tacos", label: "Tacos & Brasa" },
  { id: "micheladas", label: "Micheladas 1L" },
  { id: "antojitos", label: "Antojitos" },
  { id: "cocteles", label: "Coctelería & Mezcal" }
];

const menuItems: MenuItem[] = [
  // ==================== MICHELADAS MONUMENTALES 1L ====================
  {
    id: "mamalona-1l",
    name: "La Mamalona del Chili Negro (1L)",
    category: "micheladas",
    categoryLabel: "Michelada Monumental 1L",
    tagline: "La reina indiscutible de la cantina",
    description: "Tarro helado 1L desbordante de cerveza fría, clamato artesanal, brocheta de camarones marinados al tajín, cecina crujiente y tamarindo con gomitas enchiladas.",
    price: 44.00,
    originalPrice: 55.00,
    discountPercent: 20,
    image: "https://images.unsplash.com/photo-1582106245687-cbb466a9f07f?auto=format&fit=crop&w=1200&q=80",
    spiciness: 2,
    highlight: "1 Litro Monumental",
    monumental: true
  },
  {
    id: "ojo-rojo-1l",
    name: "Michelada Ojo Rojo Clásica (1L)",
    category: "micheladas",
    categoryLabel: "Michelada Tradicional 1L",
    tagline: "Sabor cantinero de abolengo",
    description: "Tarro congelado escarchado con sal de grano y chile piquín, clamato especial, mix secreto de salsas negras de la casa y jugo de limones recién cortados.",
    price: 28.00,
    originalPrice: 35.00,
    discountPercent: 20,
    image: "https://images.unsplash.com/photo-1618040996337-56904b7850b9?auto=format&fit=crop&w=1200&q=80",
    spiciness: 1,
    monumental: true
  },
  {
    id: "mango-habanero-1l",
    name: "Michelada Mango Habanero Loca (1L)",
    category: "micheladas",
    categoryLabel: "Michelada de Autor 1L",
    tagline: "Dulzura tropical y fuego bravío",
    description: "Puré de mango fresco, chamoy casero de flor de jamaica, escarcha de chile habanero tostado al comal y cerveza artesanal helada.",
    price: 34.00,
    originalPrice: 42.00,
    discountPercent: 19,
    image: "https://images.unsplash.com/photo-1504544750208-dc0358e63f7f?auto=format&fit=crop&w=1200&q=80",
    spiciness: 3,
    highlight: "Favorito de la Barra",
    monumental: true
  },

  // ==================== TACOS & BRASA AL CARBÓN ====================
  {
    id: "pastor-negro",
    name: "Tacos al Pastor Negro",
    category: "tacos",
    categoryLabel: "Taquería al Carbón",
    tagline: "Nuestra firma yucateca",
    description: "Cerdo marinado 24h en recado negro ancestral yucateco, cocido al mezquite con piña asada caramelizada, cebolla morada encurtida y tortillas de maíz azul.",
    price: 34.00,
    originalPrice: 42.00,
    discountPercent: 19,
    image: "https://images.unsplash.com/photo-1551504734-5ee1c4a1479b?auto=format&fit=crop&w=1200&q=80",
    spiciness: 1,
    highlight: "Plato Insignia"
  },
  {
    id: "quesabirrias",
    name: "Trilogía de Quesabirrias Ahumadas",
    category: "tacos",
    categoryLabel: "Taquería al Carbón",
    tagline: "Dorado crujiente y consomé hirviendo",
    description: "Tres tortillas de maíz pasadas por grasa de birria y doradas a la plancha, rellenas de brisket de res horneado 8 horas con queso Oaxaca fundido. Con consomé caliente.",
    price: 38.00,
    originalPrice: 48.00,
    discountPercent: 21,
    image: "https://images.unsplash.com/photo-1565299585323-38d6b0865b47?auto=format&fit=crop&w=1200&q=80",
    spiciness: 2,
    highlight: "Más Pedido"
  },
  {
    id: "carnitas-tatemadas",
    name: "Tacos de Carnitas Michoacanas",
    category: "tacos",
    categoryLabel: "Taquería Tradicional",
    tagline: "Ternura con chicharrón crujiente",
    description: "Carne confitada en paila de cobre al estilo Quiroga, tropezones de chicharrón crocante, pico de gallo rústico y salsa verde de tomatillos asados a la brasa.",
    price: 32.00,
    originalPrice: 40.00,
    discountPercent: 20,
    image: "https://images.unsplash.com/photo-1599974579688-8dbdd335c77f?auto=format&fit=crop&w=1200&q=80",
    spiciness: 1
  },
  {
    id: "tacos-gobernador",
    name: "Tacos Gobernador al Chipotle",
    category: "tacos",
    categoryLabel: "Mar & Brasa",
    tagline: "Camarones al grill y queso fundido",
    description: "Camarones salteados con rajas de chile poblano y cebolla caramelizada, sellados en costra de queso gouda con alioli cremoso de chipotle ahumado.",
    price: 42.00,
    originalPrice: 52.00,
    discountPercent: 19,
    image: "https://images.unsplash.com/photo-1624300629298-e9de39c13be5?auto=format&fit=crop&w=1200&q=80",
    spiciness: 1
  },

  // ==================== ANTOJITOS & ENTRADAS ====================
  {
    id: "guacamole-molcajete",
    name: "Guacamole en Molcajete Volcánico",
    category: "antojitos",
    categoryLabel: "Antojitos & Entradas",
    tagline: "Machacado en mesa al momento",
    description: "Aguacate Hass cremoso martajado en mortero de piedra volcánica con cebolla, cilantro, jugo de lima, granos de granada roja fresca y totopos crocantes de maíz azul.",
    price: 29.00,
    originalPrice: 36.00,
    discountPercent: 19,
    image: "https://images.unsplash.com/photo-1615870216519-2f9fa575fa5c?auto=format&fit=crop&w=1200&q=80",
    spiciness: 0,
    highlight: "En Piedra Volcánica"
  },
  {
    id: "nachos-volcanicos",
    name: "Nachos Volcánicos con Birria",
    category: "antojitos",
    categoryLabel: "Para Compartir",
    tagline: "Montaña de sabor y queso fundido",
    description: "Generosa cama de totopos caseros bañados con queso cheddar fundido, birria deshebrada de res, frijoles negros refritos, chiles jalapeños encurtidos y crema agria.",
    price: 39.00,
    originalPrice: 49.00,
    discountPercent: 20,
    image: "https://images.unsplash.com/photo-1513456852971-30c0b8199d4d?auto=format&fit=crop&w=1200&q=80",
    spiciness: 2
  },

  // ==================== COCTELERÍA & MEZCALES ====================
  {
    id: "margarita-maracuya",
    name: "Margarita Maracuyá & Jalapeño",
    category: "cocteles",
    categoryLabel: "Coctelería de Autor",
    tagline: "Ácido tropical y picor ahumado",
    description: "Tequila 100% agave azul, licor de naranja artesanal, pulpa natural de maracuyá y rodajas de chile jalapeño flameado con escarchado de sal de gusano oaxaqueña.",
    price: 32.00,
    originalPrice: 40.00,
    discountPercent: 20,
    image: "https://images.unsplash.com/photo-1556881286-fc6915169721?auto=format&fit=crop&w=1200&q=80",
    spiciness: 1,
    highlight: "Coctel de la Casa"
  },
  {
    id: "mezcalita-frutos-rojos",
    name: "Mezcalita Ahumada de Moras",
    category: "cocteles",
    categoryLabel: "Mezcales Artesanales",
    tagline: "Notas de leña y zarzamoras silvestres",
    description: "Mezcal Espadín de Oaxaca con notas ahumadas de barrica, reducción casera de zarzamoras silvestres, limón criollo y ramillete de romero encendido en la mesa.",
    price: 36.00,
    originalPrice: 45.00,
    discountPercent: 20,
    image: "https://images.unsplash.com/photo-1514362545857-3bc16c4c7d1b?auto=format&fit=crop&w=1200&q=80",
    spiciness: 0,
    highlight: "Ahumado en Mesa"
  }
];

const filteredItems = computed(() => {
  if (activeCategory.value === "todos") return menuItems;
  return menuItems.filter((i) => i.category === activeCategory.value);
});

const openWhatsAppOrder = (item: MenuItem) => {
  const text = `¡Hola El Chili Negro! Deseo ordenar: ${item.name} (S/ ${item.price.toFixed(2)}).`;
  window.open(`https://wa.me/51900514721?text=${encodeURIComponent(text)}`, "_blank");
};

// Sincronización con el navbar (#carta, #micheladas)
const handleHash = () => {
  if (typeof window === "undefined") return;
  const h = window.location.hash.replace("#", "");
  if (h === "micheladas") {
    activeCategory.value = "micheladas";
  } else if (h === "carta") {
    activeCategory.value = "todos";
  }
};

onMounted(() => {
  handleHash();
  window.addEventListener("hashchange", handleHash);
});
</script>

<template>
  <section id="carta" class="relative py-20 sm:py-28 px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto scroll-mt-24 select-none">
    <!-- Anclaje para #micheladas -->
    <div id="micheladas" class="absolute -top-24" aria-hidden="true"></div>

    <!-- ENCABEZADO DE SECCIÓN -->
    <div class="flex flex-col items-center text-center mb-12 sm:mb-16">
      <div class="inline-flex items-center gap-2 px-4 py-1.5 rounded-full bg-[#FAF5EC] border border-[#D4A373]/60 text-[#3E1F08] text-xs font-black uppercase tracking-wider mb-4 shadow-sm">
        <Flame class="w-3.5 h-3.5 text-[#DC2626] fill-[#DC2626]" />
        <span>Nuestra Selección Gastronómica</span>
      </div>

      <h2 class="text-4xl sm:text-5xl md:text-6xl font-black font-['Syne'] tracking-tight text-[#140803] mb-4">
        LA CARTA
      </h2>

      <p class="text-[#3A2213] text-sm sm:text-base max-w-2xl font-normal leading-relaxed">
        Carbón de mezquite, mariscos frescos y nuestras icónicas <strong class="text-[#991B1B] font-bold">Micheladas de 1 Litro</strong>. Elige tu favorito y pídelo al instante por WhatsApp.
      </p>

      <!-- SELECTOR DE CATEGORÍAS TIPO PILL -->
      <div class="flex items-center justify-center gap-2 sm:gap-3 flex-wrap mt-8">
        <button
          v-for="cat in categories"
          :key="cat.id"
          @click="activeCategory = cat.id"
          :class="[
            'px-5 py-2.5 rounded-full text-xs sm:text-sm font-bold tracking-wide transition-all duration-300 cursor-pointer',
            activeCategory === cat.id
              ? 'bg-[#140803] text-[#FFB800] shadow-[0_4px_16px_rgba(20,8,3,0.25)] scale-105'
              : 'bg-white text-[#140803] hover:bg-[#FAF5EC] border border-[#D4A373]/40 shadow-sm'
          ]"
        >
          {{ cat.label }}
        </button>
      </div>
    </div>

    <!-- GRID DE PLATILLOS (ESTILO CATÁLOGO BEMBOS / FAST-FOOD CON BOTÓN WHATSAPP) -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-5 sm:gap-6">
      
      <article
        v-for="item in filteredItems"
        :key="item.id"
        class="group relative flex flex-col bg-white rounded-2xl border border-[#E8DCC9] shadow-[0_4px_16px_rgba(40,15,5,0.06)] hover:shadow-[0_12px_28px_rgba(40,15,5,0.12)] hover:-translate-y-1 transition-all duration-300 overflow-hidden"
      >
        <!-- FOTO DEL PLATILLO -->
        <div class="relative w-full h-48 sm:h-52 overflow-hidden bg-[#FAF5EC]">
          <img
            :src="item.image"
            :alt="item.name"
            loading="lazy"
            class="w-full h-full object-cover object-center group-hover:scale-105 transition-transform duration-500 ease-out"
          />

          <!-- Highlight o etiqueta especial -->
          <div
            v-if="item.highlight"
            class="absolute top-3 left-3 px-2.5 py-1 rounded-full bg-black/75 backdrop-blur-md border border-amber-400/40 text-amber-300 text-[10px] font-black uppercase tracking-wider shadow-md"
          >
            {{ item.highlight }}
          </div>

          <!-- Indicador de picante con fuego -->
          <div
            v-if="item.spiciness > 0"
            class="absolute top-3 right-3 flex items-center gap-0.5 px-2 py-1 rounded-full bg-black/70 backdrop-blur-md border border-white/15"
            :title="`Picante nivel ${item.spiciness}/3`"
          >
            <Flame
              v-for="n in item.spiciness"
              :key="n"
              class="w-3 h-3 text-[#EA580C] fill-[#EA580C]"
            />
          </div>
        </div>

        <!-- CONTENIDO DE LA TARJETA -->
        <div class="p-4 sm:p-5 flex flex-col flex-1">
          
          <!-- FILA: NOMBRE DEL PLATILLO + CORAZÓN DE FAVORITO -->
          <div class="flex items-start justify-between gap-2 mb-1.5">
            <h3 class="text-base sm:text-lg font-bold font-['Syne'] text-[#140803] leading-snug group-hover:text-[#EA580C] transition-colors line-clamp-1">
              {{ item.name }}
            </h3>
            
            <!-- Corazón de favorito interactivo -->
            <button
              @click.stop="toggleFavorite(item.id)"
              :aria-label="favorites.has(item.id) ? 'Quitar de favoritos' : 'Agregar a favoritos'"
              class="text-stone-300 hover:text-red-500 transition-colors p-0.5 cursor-pointer shrink-0"
            >
              <Heart
                :class="[
                  'w-5 h-5 transition-transform duration-200 active:scale-125',
                  favorites.has(item.id) ? 'text-red-500 fill-red-500' : 'text-stone-300 hover:text-red-400'
                ]"
              />
            </button>
          </div>

          <!-- DESCRIPCIÓN CONCISA Y APETITOSA -->
          <p class="text-xs sm:text-sm text-[#735A4A] font-normal leading-relaxed line-clamp-2 mb-4 flex-1">
            {{ item.description }}
          </p>

          <!-- FILA DE PRECIO, PRECIO ANTERIOR Y PORCENTAJE DE DESCUENTO -->
          <div class="flex items-center gap-2 flex-wrap mb-3 pt-2.5 border-t border-[#F2E8D7]">
            <!-- Precio actual grande -->
            <div class="flex items-baseline gap-1">
              <span class="text-sm font-extrabold text-[#140803]">S/</span>
              <span class="text-xl sm:text-2xl font-black font-['Plus_Jakarta_Sans',sans-serif] text-[#140803] tracking-tight">
                {{ item.price.toFixed(2) }}
              </span>
            </div>

            <!-- Precio anterior tachado -->
            <span class="text-xs sm:text-sm text-[#DC2626] line-through font-bold font-['Plus_Jakarta_Sans',sans-serif] tracking-tight">
              S/{{ item.originalPrice.toFixed(2) }}
            </span>

            <!-- Badge rojo de descuento -->
            <span class="bg-[#DC2626] text-white text-[10px] font-black px-1.5 py-0.5 rounded shadow-xs">
              -{{ item.discountPercent }}%
            </span>
          </div>

          <!-- BOTÓN DE WHATSAPP DIRECTAMENTE DEBAJO DEL PRECIO (EN VEZ DEL BOTÓN MÁS) -->
          <button
            @click="openWhatsAppOrder(item)"
            class="w-full py-2.5 px-4 rounded-xl bg-[#25D366] hover:bg-[#20bd5a] text-white font-black font-['Syne',sans-serif] text-xs sm:text-sm tracking-wide flex items-center justify-center gap-2 shadow-[0_4px_12px_rgba(37,211,102,0.30)] hover:shadow-[0_6px_18px_rgba(37,211,102,0.45)] hover:scale-[1.02] active:scale-[0.98] transition-all duration-200 cursor-pointer"
          >
            <!-- Ícono Oficial de WhatsApp SVG -->
            <svg class="w-4 h-4 fill-white shrink-0" viewBox="0 0 24 24">
              <path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946.003-6.556 5.338-11.891 11.893-11.891 3.181.001 6.167 1.24 8.413 3.488 2.245 2.248 3.481 5.236 3.48 8.414-.003 6.557-5.338 11.892-11.893 11.892-1.99-.001-3.951-.5-5.688-1.448l-6.305 1.654zm6.597-3.807c1.676.995 3.276 1.591 5.392 1.592 5.448 0 9.886-4.434 9.889-9.885.002-5.462-4.415-9.89-9.881-9.892-5.452 0-9.887 4.434-9.889 9.884-.001 2.225.651 3.891 1.746 5.634l-.999 3.648 3.742-.981zm11.387-5.464c-.074-.124-.272-.198-.57-.347-.297-.149-1.758-.868-2.031-.967-.272-.099-.47-.149-.669.149-.198.297-.768.967-.941 1.165-.173.198-.347.223-.644.074-.297-.149-1.255-.462-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.297-.347.446-.521.151-.172.2-.296.3-.495.099-.198.05-.372-.025-.521-.075-.148-.669-1.611-.916-2.206-.242-.579-.487-.501-.669-.51l-.57-.01c-.198 0-.52.074-.792.372s-1.04 1.016-1.04 2.479 1.065 2.876 1.213 3.074c.149.198 2.095 3.2 5.076 4.487.709.306 1.263.489 1.694.626.712.226 1.36.194 1.872.118.571-.085 1.758-.719 2.006-1.413.248-.695.248-1.29.173-1.414z"/>
            </svg>
            <span>Pedir por WhatsApp</span>
          </button>

        </div>
      </article>

    </div>

    <!-- PIE DEL MENÚ -->
    <div class="mt-16 sm:mt-20 text-center border-t border-[#D4A373]/40 pt-10 flex flex-col items-center">
      <p class="text-[#3A2213] text-xs sm:text-sm max-w-xl font-normal mb-3">
        Todos nuestros platillos se preparan al momento con carbón de mezquite y maíz nixtamalizado. Precios en Soles (S/) incluyen impuestos de ley.
      </p>
      <a
        href="https://wa.me/51900514721?text=Hola%20El%20Chili%20Negro,%20deseo%20hacer%20una%20consulta%20sobre%20la%20carta"
        target="_blank"
        rel="noopener noreferrer"
        class="inline-flex items-center gap-2 text-xs sm:text-sm text-[#991B1B] hover:text-[#7F1D1D] font-bold tracking-wide underline underline-offset-4 decoration-[#991B1B]/40 transition-colors"
      >
        <span>¿Consultas especiales o pedidos corporativos? Escríbenos directamente</span>
        <ArrowUpRight class="w-3.5 h-3.5" />
      </a>
    </div>

  </section>
</template>

<style scoped>
h2, h3 {
  letter-spacing: -0.025em;
}
</style>
