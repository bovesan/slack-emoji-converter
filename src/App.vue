<template>
  <v-app>
    <v-main>
      <Converter />
    </v-main>
    <v-footer class="d-block text-grey flex-grow-0 text-caption">The conversion happens on your device and no data is transferred anywhere else. © {{ new Date().getFullYear() }} &nbsp;<a href="https://bovesan.com/" style="color: inherit;">Bengt Ove Sannes</a></v-footer>
  </v-app>
</template>

<script setup lang="ts">
import Converter from '@/components/Converter.vue'
</script>
