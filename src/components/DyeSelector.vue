<template>
  <div v-if="active">
    <v-color-picker
        v-model="dyeRgb"
        :modes="modes"
        :swatches="swatches"
        show-swatches
    />

    <v-btn @click="applyDye">
      apply dye
    </v-btn>
  </div>
   <div v-else>
    <div>
      <color-display v-if="inputDyeRgb" :color-rgb="inputDyeRgb" />
    </div>

    <v-btn @click="active = true">
      change dye
    </v-btn>
  </div>
</template>

<script>
import ColorDisplay from '@/components/ColorDisplay.vue'
export default {
  name: 'DyeSelector',
  components: {
      ColorDisplay
  },
  props: {
    inputDyeRgb: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      active: false,
      dyeRgb: JSON.parse(JSON.stringify(this.inputDyeRgb ? this.inputDyeRgb : { r: 176, g: 188, b: 54 })),
      modes: ['rgb', 'hex'],
      swatches: [
        [
          { r: 67, g: 93, b: 80 }, //dark green
          { r: 176, g: 188, b: 54 }, //apple green
          { r: 112, g: 109, b: 55 }, //woodland
          { r: 74, g: 90, b: 73 }, //chrysler green

          { r: 88, g: 104, b: 76 }, //deep forest green
          { r: 98, g: 92, b: 72 }, //rain forest
          { r: 85, g: 115, b: 61}, //snow pea

          { r: 96, g: 113, b: 59}, //alligator scales
          { r: 104, g: 110, b: 91}, //crocodile green
          { r: 102, g: 124, b: 61}, //kiwi kiss
          { r: 127, g: 140, b: 78}, //green thumb
          { r: 125, g: 123, b: 86}, //mustard green

          { r: 93, g: 103, b: 74}, //moss green
          { r: 110, g: 107, b: 87} //moss bed
        ]
      ]
    };
  },
  computed: {
    dyeHex() {
      if (this.dyeRgb && this.dyeRgb.r !== null) {
        return this.rgbToHex(this.dyeRgb.r, this.dyeRgb.g, this.dyeRgb.b);
      } else {
        return null;
      }
    },
    rgbDisplay() {
      if (!this.dyeRgb || this.dyeRgb.r === null) {
        return null;
      } else {
        return 'rgb(' + this.dyeRgb.r + ', ' + this.dyeRgb.g + ', ' + this.dyeRgb.b + ')';
      }
    }
  },
  methods: {
    rgbToHex(r, g, b) {
      return '#' + this.componentToHex(r) + this.componentToHex(g) + this.componentToHex(b);
    },
    componentToHex(c) {
      const hex = c.toString(16);

      return hex.length === 1 ? '0' + hex : hex;
    },
    applyDye() {
      this.active = false;
      this.$emit('changed', this.dyeRgb);
    }
  }
}
</script>
