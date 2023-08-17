<template>
  <div>
    <dropper-selector @changed="inputRgb = $event" />

    <final-canvas v-show="dyeRgb && dyeRgb.r !== null" :dye-rgb="dyeRgb" />
<!--    <img v-show="!dyeRgb" src="../assets/acu.jpg" class="text-center" />-->

    <dye-selector :input-dye-rgb="dyeRgb" @changed="dyeRgb = $event" />
  </div>
</template>

<script>
import DropperSelector from '@/components/DropperSelector.vue'
import DyeSelector from '@/components/DyeSelector.vue'
import FinalCanvas from '@/components/FinalCanvas.vue'

export default {
  name: 'ColorMixer',
  components: { DropperSelector, DyeSelector, FinalCanvas },
  data() {
    return {
      inputRgb: null,
      dyeRgb: null
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
