<template>
  <section class="draw-image-box">
    <canvas ref="refCanvas" id="canvas" :width="imgWidth" :height="imgHeight" v-show="false"></canvas>
    <img ref="refImage" id="image" :src="imgSrc" crossorigin="Anonymous" @load="_imgLoad" v-show="false" />
    <vueCropper ref="cropper" :img="option.img"></vueCropper>
  </section>
</template>
<script>
import { VueCropper } from 'vue-cropper';

export default {
  name: 'DrawImage',
  components: {
    VueCropper,
  },
  props: {
    // img to edit
    editImgSrc: {
      type: String,
      default: ''
    },
    // original img
    cleanImgSrc: {
      type: String,
      default: ''
    },
    // img format
    type: {
      type: String,
      default: 'image/png'
    },
    // img quality
    quality: {
      type: Number,
      default: 1
    }
  },
  data() {
    return {
      refCanvas: '',
      domCanvas: '',
      ctxCanvas: '',
      imgWidth: 0,
      imgHeight: 0,
      refImage: '',
      imgSrc: '',
      option: {
        img: '',
      },
      drawArray: [],
      blobData: '',
      base64Data: '',
    }
  },
  watch: {
    editImgSrc(val) {
      this.initDraw(val);
    },
  },
  mounted() {
    this.refCanvas = this.$refs.refCanvas;
    this.ctxCanvas = this.refCanvas.getContext('2d');
    this.domCanvas = document.getElementById("canvas");
    this.refImage = this.$refs.refImage;
  },
  methods: {
    startDraw() {
      this._startCrop();
    },

    stopDraw() {
      this._canvasOnDraw();
      this._stopCrop();
      this._clearCrop();
    },

    initDraw(backUrl = this.cleanImgSrc) {
      this.option.img = backUrl;
      this.imgSrc = backUrl;
      this.drawArray = [];
      this.blobData = backUrl;
      this.base64Data = backUrl;
      this._stopCrop();
      this._clearCrop();
    },

    getBlobData() {
      return this.blobData;
    },

    getBase64Data() {
      this.base64Data = this.domCanvas.toDataURL(this.type, this.quality);
      return this.base64Data;
    },

    _imgLoad(e) {
      this.imgWidth = e.path[0].naturalWidth;
      this.imgHeight = e.path[0].naturalHeight;
    },

    // canvas draw
    _canvasOnDraw() {
      // put img
      this.ctxCanvas.drawImage(
        this.refImage,
        0,
        0,
        this.imgWidth,
        this.imgHeight,
        0,
        0,
        this.imgWidth,
        this.imgHeight
      );

      // get the ratio of the screenshot box and image zoom
      let imgPos = this._getImgAxis();
      let cutPos = this._getCropAxis();

      try {
        let rate = this.imgHeight / (imgPos.y2 - imgPos.y1);

        let xxx = (cutPos.x1 - imgPos.x1) * rate;
        let yyy = (cutPos.y1 - imgPos.y1) * rate;

        let cutW = (cutPos.x2 - cutPos.x1) * rate;
        let cutH = (cutPos.y2 - cutPos.y1) * rate;

        this.drawArray.push({ xxx, yyy, cutW, cutH });
      } catch (error) {
        console.log('Calculate the coordinates error', error);
      }

      // draw rect
      this.ctxCanvas.strokeStyle = 'red';
      this.ctxCanvas.lineWidth = 10;
      this.drawArray.forEach(item => {
        let { xxx, yyy, cutW, cutH } = item || {};
        this.ctxCanvas.strokeRect(xxx, yyy, cutW, cutH);
      })
      this.domCanvas.toBlob((blob) => {
        this.blobData = blob;
        this.option.img = URL.createObjectURL(blob);
      }, this.type, this.quality);
    },

    _startCrop() {
      this.$refs.cropper.startCrop();
    },

    _stopCrop() {
      this.$refs.cropper.stopCrop();
    },

    _clearCrop() {
      this.$refs.cropper.clearCrop();
    },

    _getImgAxis() {
      return this.$refs.cropper.getImgAxis();
    },

    _getCropAxis() {
      return this.$refs.cropper.getCropAxis();
    },
  }
}
</script>
<style scoped>
.draw-image-box {
  width: 100%;
  height: 100%;
}
</style>
