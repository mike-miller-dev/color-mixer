<template>
  <span :style="{ color: hexCode }">{{ hexCode }} {{ rgbDisplay }}</span>
</template>

<script>
export default {
  name: 'ColorDisplay',
  props: {
    colorRgb: {
      type: Object,
      default: null
    },
    colorHex: {
      type: String,
      default: null
    }
  },
  computed: {
    hexCode() {
      if (this.colorHex) {
        return this.colorHex;
      } else if (this.colorRgb && this.colorRgb.r) {
        return this.rgbToHex(this.colorRgb.r, this.colorRgb.g, this.colorRgb.b);
      } else {
        return null;
      }
    },
    rgbDisplay() {
      if (!this.colorRgb || this.colorRgb.r === null) {
        return null;
      } else {
        return 'rgb(' + this.colorRgb.r + ', ' + this.colorRgb.g + ', ' + this.colorRgb.b + ')';
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
    }
  }
}
</script>
