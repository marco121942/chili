<script setup lang="ts">
import { MapPin, Flame, Navigation, ExternalLink } from "lucide-vue-next";

const mapQuery = encodeURIComponent("El Chili Negro Francisco Solano 489 Huancayo Peru");
const googleMapsEmbedUrl = `https://maps.google.com/maps?q=${mapQuery}&t=&z=16&ie=UTF8&iwloc=&output=embed`;
const googleMapsDirectionsUrl = `https://www.google.com/maps/dir/?api=1&destination=${encodeURIComponent("Francisco Solano 489, Huancayo, Peru")}`;
</script>

<template>
  <section id="ubicacion" class="relative py-16 sm:py-24 px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto scroll-mt-24 select-none">
    
    <!-- RESPLANDOR DE FONDO CÁLIDO -->
    <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[500px] h-[500px] bg-amber-400/10 rounded-full blur-3xl pointer-events-none"></div>

    <!-- ENCABEZADO DE SECCIÓN -->
    <div class="flex flex-col items-center text-center mb-8 sm:mb-12">
      <div class="inline-flex items-center gap-2 px-4 py-1.5 rounded-full bg-[#FAF5EC] border border-[#D4A373]/60 text-[#3E1F08] text-xs font-black uppercase tracking-wider mb-4 shadow-sm">
        <MapPin class="w-3.5 h-3.5 text-[#DC2626]" />
        <span>Visítanos en Huancayo</span>
      </div>

      <h2 class="text-3xl sm:text-5xl lg:text-6xl font-black font-['Syne'] tracking-tight text-[#140803] uppercase mb-3">
        ENCUÉNTRANOS EN EL MAPA
      </h2>

      <p class="text-[#3A2213] text-sm sm:text-base max-w-2xl font-normal leading-relaxed">
        Francisco Solano 489, San Carlos, Huancayo. Ven a vivir la auténtica experiencia mexicana con tacos al fuego y micheladas de 1L.
      </p>
    </div>

    <!-- GOOGLE MAPS A ANCHO COMPLETO -->
    <div class="relative w-full h-[450px] sm:h-[520px] lg:h-[580px] rounded-3xl overflow-hidden border-2 border-amber-400/35 shadow-[0_20px_50px_rgba(40,15,5,0.15)] bg-[#FAF5EC]">
      
      <!-- Badge flotante sobre el mapa -->
      <div class="absolute top-4 left-4 z-10 inline-flex items-center gap-1.5 px-4 py-2 rounded-full bg-[#140803]/90 backdrop-blur-md border border-amber-400/40 text-amber-300 text-xs font-black uppercase tracking-wider shadow-lg">
        <Flame class="w-3.5 h-3.5 text-[#EA580C] fill-[#EA580C]" />
        <span>Marcador Oficial: El Chili Negro</span>
      </div>

      <!-- Botón flotante para abrir ruta en Google Maps -->
      <a
        :href="googleMapsDirectionsUrl"
        target="_blank"
        rel="noopener noreferrer"
        class="absolute bottom-5 right-5 z-10 inline-flex items-center gap-2 px-4 py-2.5 rounded-2xl bg-gradient-to-r from-[#B91C1C] via-[#EA580C] to-[#D97706] hover:brightness-110 active:scale-95 text-white font-black font-['Syne'] text-xs sm:text-sm tracking-wide shadow-[0_4px_18px_rgba(234,88,12,0.4)] transition-all cursor-pointer"
      >
        <Navigation class="w-4 h-4" />
        <span>Cómo Llegar</span>
        <ExternalLink class="w-3.5 h-3.5 opacity-80" />
      </a>

      <!-- Iframe de Google Maps a ancho completo -->
      <iframe
        :src="googleMapsEmbedUrl"
        class="w-full h-full border-0"
        loading="lazy"
        referrerpolicy="no-referrer-when-downgrade"
        title="Ubicación de El Chili Negro en Google Maps"
      ></iframe>

    </div>

  </section>
</template>

<style scoped>
</style>
