<template>
  <section class="draw-image-box">
    <canvas ref="refCanvas" id="canvas" :width="imgWidth" :height="imgHeight" v-show="false"></canvas>
    <img ref="refImage" id="image" :src="imgSrc" crossorigin="Anonymous" @load="_uploadImgLoad" v-show="false" />
    <vueCropper ref="cropper" :img="option.img"></vueCropper>
  </section>
</template>
<script>
import { VueCropper } from 'vue-cropper'

export default {
  name: 'DrawImage',
  components: {
    VueCropper,
  },
  props: {
    // 要编辑的图
    editImgSrc: {
      type: String,
      default: ''
    },
    // 原图
    cleanImgSrc: {
      type: String,
      default: ''
    },
  },
  data() {
    return {
      // canvas的配置部分
      refCanvas: '',
      domCanvas: '',
      ctxCanvas: '',
      // image的真实宽高
      imgWidth: 0,
      imgHeight: 0,
      // img配置部分
      refImage: '',
      imgSrc: '',
      // canvas宽高
      canvasWidth: 0,
      canvasHeight: 0,
      // Cropper 配置
      option: {
        img: '',
      },
      // 裁剪框的坐标
      cutPos: {},
      // 图片坐标，放大缩小会变
      imgPos: {},
      // 多次画图记录
      drawArray: [],
      blobData: ''
    }
  },
  watch: {
    editImgSrc(val) {
      this.initDraw(val)
    },
  },
  mounted() {
    this.refCanvas = this.$refs.refCanvas;
    this.ctxCanvas = this.refCanvas.getContext('2d');
    this.domCanvas = document.getElementById("canvas");
    this.refImage = this.$refs.refImage;
  },
  methods: {
    // 开始框选
    startDraw() {
      this._startCrop()
    },

    // 结束框选
    stopDraw() {
      this._canvasOnDraw(this.imgWidth, this.imgHeight)
      this._stopCrop()
      this._clearCrop()
    },

    initDraw(backUrl = this.cleanImgSrc) {
      this.option.img = backUrl;
      this.imgSrc = backUrl
      this.drawArray = []
      // TODO
      this.blobData = backUrl
      this._stopCrop()
      this._clearCrop()
    },

    getBlobData() {
      this.$emit('saveBlobData', this.blobData)
    },

    _uploadImgLoad(e) {
      this.imgWidth = e.path[0].naturalWidth;
      this.imgHeight = e.path[0].naturalHeight;
      this._setCanvasSize()
    },

    _setCanvasSize() {
      try {
        if (this.imgWidth >= this.imgHeight) {
          this.canvasWidth = document.getElementsByClassName("draw-image-box")[0].offsetWidth
          this.canvasHeight = (this.canvasWidth * this.imgHeight) / this.imgWidth
        } else {
          this.canvasHeight = document.getElementsByClassName("draw-image-box")[0].offsetHeight
          this.canvasWidth = (this.canvasHeight * this.imgWidth) / this.imgHeight
        }

      } catch (error) {
        console.log('_setCanvasSize', error)
      }
    },

    // canvas绘图部分
    _canvasOnDraw() {
      // 放图片
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


      this._getImgAxis()
      this._getCropAxis()

      try {
        let rate = (this.imgPos.y2 - this.imgPos.y1) / this.canvasHeight
        let scale = this.imgWidth / this.canvasWidth

        let cutW = (this.cutPos.x2 - this.cutPos.x1) / rate
        let cutH = (this.cutPos.y2 - this.cutPos.y1) / rate

        let lll = (this.cutPos.x1 - this.imgPos.x1) / rate
        let rrr = (this.cutPos.y1 - this.imgPos.y1) / rate

        this.drawArray.push({ lll, rrr, cutW, cutH, scale })
      } catch (error) {
        console.log('计算坐标error', error)
      }

      // 画矩形
      this.ctxCanvas.strokeStyle = 'red';
      this.ctxCanvas.lineWidth = 10;
      this.drawArray.forEach(item => {
        let { lll, rrr, cutW, cutH, scale } = item
        this.ctxCanvas.strokeRect(lll * scale, rrr * scale, cutW * scale, cutH * scale);
      })
      // 输出图片，替换
      let url = ''
      this.domCanvas.toBlob((blob) => {
        url = URL.createObjectURL(blob);
        this.blobData = blob;
        this.option.img = url
      }, 'image/jpeg', 1);
    },

    // 开始截图
    _startCrop() {
      this.$refs.cropper.startCrop()
    },
    // 停止截图
    _stopCrop() {
      this.$refs.cropper.stopCrop()
    },
    // 清除截图
    _clearCrop() {
      this.$refs.cropper.clearCrop()
    },
    // 获取图片基于容器的坐标点
    _getImgAxis() {
      this.imgPos = this.$refs.cropper.getImgAxis()
    },
    // 获取截图框基于容器的坐标点
    _getCropAxis() {
      this.cutPos = this.$refs.cropper.getCropAxis()
    },
  }
}
</script>
<style scoped>
.draw-image-box {
  width: 100%;
  height: 800px;
}
</style>
