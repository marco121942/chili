<script setup lang="ts">
import { ref } from "vue";
import HeaderNav from "./components/HeaderNav.vue";
import PageLoader from "./components/PageLoader.vue";
import PromoSlider from "./components/PromoSlider.vue";
import CompanyStory from "./components/CompanyStory.vue";
import MenuSection from "./components/MenuSection.vue";
import LocationMapSection from "./components/LocationMapSection.vue";

const isLoaded = ref(false);
</script>

<template>
  <!-- Pantalla de carga con la Catrina tradicional mexicana -->
  <PageLoader @loaded="isLoaded = true" />

  <div class="min-h-screen bg-gradient-to-b from-[#FFFFFF] via-[#FAF5EC] to-[#F2E8D7] text-[#140803] relative overflow-x-hidden selection:bg-[#140803] selection:text-[#FFB800]">
    <!-- Navbar con la cuerda y banderillas de papel picado fijo que acompaña el scroll de la web -->
    <div
      :class="[
        'fixed top-0 left-0 right-0 z-40 transition-all duration-700 ease-out transform pointer-events-none',
        isLoaded ? 'translate-y-0 opacity-100' : '-translate-y-10 opacity-0'
      ]"
    >
      <HeaderNav />
    </div>

    <!-- Slider Panorámico de Promociones (Estilo KFC) DIRECTAMENTE debajo del Header -->
    <main
      :class="[
        'relative z-10 pt-28 sm:pt-36 md:pt-40 transition-all duration-700 delay-150 transform',
        isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-4'
      ]"
    >
      <!-- Resplandor solar de hacienda y calor de maíz dorado de fondo -->
      <div
        class="absolute top-1/4 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[850px] h-[600px] bg-gradient-to-tr from-[#FFB800]/18 via-[#F59E0B]/12 to-[#E64A19]/8 blur-[160px] rounded-full pointer-events-none"
      ></div>

      <PromoSlider />
    </main>

    <!-- Sección de Presentación de la Empresa (Video + Diálogo) -->
    <div
      :class="[
        'transition-all duration-700 delay-200 transform',
        isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-6'
      ]"
    >
      <CompanyStory />
    </div>

    <!-- Sección de la Carta de Productos -->
    <div
      :class="[
        'transition-all duration-700 delay-300 transform',
        isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-8'
      ]"
    >
      <MenuSection />
    </div>

    <!-- Sección de Ubicación con Google Maps y Marcador -->
    <div
      :class="[
        'transition-all duration-700 delay-400 transform',
        isLoaded ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-8'
      ]"
    >
      <LocationMapSection />
    </div>
  </div>
</template>