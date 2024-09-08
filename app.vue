<template>
  <div>
    <v-app>
      <AppHeader @scroll-to-section="scrollToSection" />
      <NuxtPage  ref="nuxtPageRef" />
    </v-app>
  </div>
</template>

<script setup lang="ts">

interface PageComponent {
  scrollToSection: (section: string) => void;
}

interface BodyComponent {
  pageRef: PageComponent;
}

const nuxtPageRef = ref<BodyComponent| null>(null)

function scrollToSection(section: string) {
  if (!nuxtPageRef.value || !nuxtPageRef.value.pageRef) {
    console.log('Page ref not available');
    return;
  }
  const bodyPage = nuxtPageRef.value.pageRef
  bodyPage.scrollToSection(section)
}
</script>

<style lang="sass">
.limit-width
  max-width: 1400px
</style>
