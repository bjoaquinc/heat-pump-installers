<template>
  <v-app-bar app color="white" height="90">
    <v-toolbar color="white" class="limit-width mx-auto">
      <template #prepend>
        <v-img src="~/assets/img/logo.svg" class="ml-4" :width="300"></v-img>
      </template>

      <div v-if="!isMobile">

        <v-btn
          @click="scrollToSection('contractors')"
          flat
          variant="text"
          color="primary"
          class="text-none mr-4"
          size="large"
        >
          Contractors
        </v-btn>

        <v-btn
          @click="scrollToSection('about')"
          flat
          variant="text"
          color="primary"
          class="text-none mr-4"
          size="large"
        >
          About
        </v-btn>

        <v-btn
          variant="flat"
          rounded="lg"
          color="primary"
          class="mr-6"
          size="large"
        >
          CONTACT US
        </v-btn>
      </div>
      <template #append v-if="isMobile">
        <v-btn
          icon
          @click="drawer = !drawer"
          class="mr-4"
        >
          <v-icon>mdi-menu</v-icon>
        </v-btn>
      </template>
    </v-toolbar>
  </v-app-bar>
  <v-navigation-drawer v-model="drawer" location="right">
    <v-list nav>
      <v-list-item @click="() => {
        scrollToSection('contractors')
        drawer = false
      }" prepend-icon="mdi-tools" title="Contractors" value="inbox" class="text-subtitle-1"></v-list-item>
      <v-list-item @click="() => {
        scrollToSection('about')
        drawer = false
      }" prepend-icon="mdi-account-multiple-outline" title="About Us" class="text-subtitle-1"></v-list-item>
      <v-list-item title="CONTACT US" value="inbox" class="text-subtitle-1 bg-primary"></v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>

<script setup lang="ts">

const { isMobile } = useDevice()
const drawer = ref(false)

const emits = defineEmits<{
  scrollToSection: [section: string],
}>()

const scrollToSection = (section: string) => {
  emits('scrollToSection', section)
}
</script>
