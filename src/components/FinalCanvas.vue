<template>
  <div>
    <div v-show="!loading">
      <canvas id="canvas" width="750" height="920"></canvas>
    </div>

    <div v-show="loading" class="loading-box">
      <v-progress-circular
        indeterminate
        color="primary"
        :size="50"
      />
    </div>

  </div>
</template>

<script>
export default {
  name: 'FinalCanvas',
  props: {
    dyeRgb: {
      type: Object,
      default: null
    },
    dyeWeight: {
      type: Number,
      default: 0.8
    }
  },
  data() {
    return {
      image: null,
      pageCanvas: null,
      myCanvasContext: null,
      originalImage: null,
      loading: false
    };
  },
  computed: {
    inputRgb() {
      return this.hexToRgb(this.sRGBHex);
    },
    inputCymk() {
      if (this.inputRgb && this.inputRgb.r !== null) {
        return this.rgbToCymk(this.inputRgb.r, this.inputRgb.g, this.inputRgb.b);
      } else {
        return null;
      }
    },
    dyeHex() {
      return this.rgbToHex(this.dyeRgb.r, this.dyeRgb.g, this.dyeRgb.b);
    },
    mixedResult() {
      if (this.inputRgb && this.inputRgb.r !== null && this.dyeRgb && this.dyeRgb.r !== null) {
        return this.mix(this.inputRgb, this.dyeRgb);
      } else {
        return null;
      }
    }
  },
  watch: {
    dyeRgb: function (newVal, oldVal) {
      this.applyDye(newVal);
    }
  },
  methods: {
    async applyDye(dyeColor) {
      if (!dyeColor || dyeColor.r === null) {
        return;
      }
      this.loading = true;
      await this.sleep(1);
      if (!this.image) {
        await this.loadCanvasAndImage();
      }

      const newImage = this.myCanvasContext.getImageData(0, 0, this.image.width, this.image.height);

      for (let i = 0; i < this.originalImage.data.length; i += 4) {
        const oldColor = {
          r: this.originalImage.data[i],
          g: this.originalImage.data[i + 1],
          b: this.originalImage.data[i + 2]
        };
        const newColor = this.mix(oldColor, dyeColor);

        newImage.data[i] = newColor.r;
        newImage.data[i + 1] = newColor.g;
        newImage.data[i + 2] = newColor.b;
      }

      this.myCanvasContext.putImageData(newImage, 0, 0);
      this.image.src = this.pageCanvas.toDataURL();
      this.loading = false;
    },
    mix(originalColor, dyeColor) {
      const cymk1 = this.rgbToCymk(originalColor.r, originalColor.g, originalColor.b);
      const cymk2 = this.rgbToCymk(dyeColor.r, dyeColor.g, dyeColor.b);

      const cymk3 = {
        c: this.pickBolderColor(cymk1.c, cymk2.c),
        m: this.pickBolderColor(cymk1.m, cymk2.m),
        y: this.pickBolderColor(cymk1.y, cymk2.y),
        k: this.pickBolderColor(cymk1.k, cymk2.k)
      };

      return this.toRgba(cymk3);
    },
    pickBolderColor(originalColor, dyeColor) {
      if (originalColor > dyeColor) {
        return originalColor;
      } else {
        return (1 - this.dyeWeight) * originalColor + this.dyeWeight * dyeColor;
      }
    },
    toRgba(cmyk) {
      let R = cmyk.c * (1.0 - cmyk.k) + cmyk.k;
      let G = cmyk.m * (1.0 - cmyk.k) + cmyk.k;
      let B = cmyk.y * (1.0 - cmyk.k) + cmyk.k;

      R = Math.round((1.0 - R) * 255.0 + 0.5);
      G = Math.round((1.0 - G) * 255.0 + 0.5);
      B = Math.round((1.0 - B) * 255.0 + 0.5);

      return { r: R, g: G, b: B };
    },
    rgbToCymk(r, g, b) {
      let cyan = 255 - r;
      let magenta = 255 - g;
      let yellow = 255 - b;
      const black = Math.min(cyan, magenta, yellow);

      cyan = r === 0 ? 0 : (cyan - black) / (255 - black);
      magenta = g === 0 ? 0 : (magenta - black) / (255 - black);
      yellow = b === 0 ? 0 : (yellow - black) / (255 - black);

      return {
        c: cyan,
        m: magenta,
        y: yellow,
        k: black / 255
      };
    },
    async loadCanvasAndImage() {
      this.image = new Image();
      this.image.src = "../src/assets/acu.jpg";
      this.pageCanvas = document.getElementById('canvas');

      this.myCanvasContext = this.pageCanvas.getContext('2d', { willReadFrequently: true });

      await this.image.decode();

      this.pageCanvas.width = this.image.width;
      this.pageCanvas.height = this.image.height;

      this.myCanvasContext.drawImage(this.image, 0, 0);

      this.originalImage = this.myCanvasContext.getImageData(0, 0, this.image.width, this.image.height);
    },
    sleep(milliseconds) {
      return new Promise(resolve => this.timeOut = setTimeout(resolve, milliseconds));
    },
  }
}
</script>
<style lang="scss" scoped>
  .loading-box {
    width: 750px;
    height: 920px;
  }
</style>
