<script setup>
</script>

<template>
  <VCard class="position-relative">
    <VCardText>
      <div class="mb-2">
        <h5 class="text-h5">
          Bienvenido! <span class="text-high-emphasis">🎉</span>
        </h5>
        <div class="text-body-1">
          Esta es la API para Códigos de Países
        </div>
      </div>
    <div style="display: flex;width: 100%;flex-direction: column;align-items: center;justify-content: center;">
        <h4 class="text-h1 text-primary">
        +51
      </h4>
      <div class="text-body-1 mb-2">
        Perú 🇵🇪
      </div>
    </div>
      <!-- <VBtn size="small">
        View Sales
      </VBtn> -->
    </VCardText>

    <!-- Trophy -->
    <!-- <VImg
      :src="trophy"
      class="trophy"
    /> -->
  </VCard>
</template>

<style lang="scss">
.v-card .trophy {
  position: absolute;
  inline-size: 5.188rem;
  inset-block-end: 1.25rem;
  inset-inline-end: 1.25rem;
}
</style>
