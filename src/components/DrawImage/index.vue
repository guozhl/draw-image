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
    // 图片格式
    type: {
      type: String,
      default: 'image/png'
    },
    // 图片质量
    quality: {
      type: Number,
      default: 1
    }
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
      // Cropper 配置
      option: {
        img: '',
      },
      // 多次画图记录
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
    // 开始框选
    startDraw() {
      this._startCrop();
    },

    // 结束框选
    stopDraw() {
      this._canvasOnDraw();
      this._stopCrop();
      this._clearCrop();
    },

    initDraw(backUrl = this.cleanImgSrc) {
      this.option.img = backUrl;
      this.imgSrc = backUrl;
      this.drawArray = [];
      // TODO 不要改变数据格式
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

      // 获取截图框和图片放大的比例
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
        console.log('计算坐标error', error);
      }

      // 画矩形
      this.ctxCanvas.strokeStyle = 'red';
      this.ctxCanvas.lineWidth = 10;
      this.drawArray.forEach(item => {
        let { xxx, yyy, cutW, cutH } = item || {};
        /**
         * 矩形起点的 x 轴坐标
         * 矩形起点的 y 轴坐标
         * 矩形的宽度。正值在右侧，负值在左侧
         * 矩形的高度。正值在下，负值在上
         */
        this.ctxCanvas.strokeRect(xxx, yyy, cutW, cutH);
      })
      // 输出图片，替换
      this.domCanvas.toBlob((blob) => {
        this.blobData = blob;
        this.option.img = URL.createObjectURL(blob);
      }, this.type, this.quality);
    },

    // 开始截图
    _startCrop() {
      this.$refs.cropper.startCrop();
    },
    // 停止截图
    _stopCrop() {
      this.$refs.cropper.stopCrop();
    },
    // 清除截图
    _clearCrop() {
      this.$refs.cropper.clearCrop();
    },
    // 获取图片基于容器的坐标点
    _getImgAxis() {
      return this.$refs.cropper.getImgAxis();
    },
    // 获取截图框基于容器的坐标点
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
