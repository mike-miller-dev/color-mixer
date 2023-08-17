<template>
  <div v-if="isSupported">
    <div>Input Color: <color-display :color-hex="sRGBHex" :color-rgb="inputRgb" /></div>
      <v-btn @click="open">
        Open Eye Dropper
      </v-btn>
  </div>
  <div v-else>
    <span>Not Supported by Your Browser</span>
  </div>
</template>

<script>
import ColorDisplay from '@/components/ColorDisplay.vue'
import { useEyeDropper } from '@vueuse/core'

export default {
  name: 'DropperSelector',
  components: { ColorDisplay },
  setup() {
    const { isSupported, sRGBHex, open } = useEyeDropper()

    return {
      isSupported,
      open,
      sRGBHex
    };
  },
  computed: {
    inputRgb() {
      return this.hexToRgb(this.sRGBHex);
    },
    inputCymk() {
      if (this.inputRgb && this.inputRgb.r) {
        return this.rgbToCymk(this.inputRgb.r, this.inputRgb.g, this.inputRgb.b);
      } else {
        return null;
      }
    }
  },
  watch: {
    inputRgb() {
      this.$emit('changed', this.inputRgb)
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
    hexToRgb(hex) {
      const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);

      return result
        ? {
            r: parseInt(result[1], 16),
            g: parseInt(result[2], 16),
            b: parseInt(result[3], 16)
          }
        : null;
    },
    rgbToCymk(r, g, b) {
      let cyan = 255 - r;
      let magenta = 255 - g;
      let yellow = 255 - b;
      const black = Math.min(cyan, magenta, yellow);

      cyan = (cyan - black) / (255 - black);
      magenta = (magenta - black) / (255 - black);
      yellow = (yellow - black) / (255 - black);

      return {
        c: cyan,
        m: magenta,
        y: yellow,
        k: black / 255
      };
    }
  }
}
</script>
