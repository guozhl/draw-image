<template>
  <div id="app">
    <div>
      <button @click="startDraw">框选位置</button>
      <button @click="stopDraw">结束框选</button>
      <button @click="clearDraw">清除框选</button>
      <button @click="submitPage">保 存</button>
    </div>
    <div style="width: 500px; height: 800px;">
      <draw-image ref="drawImage" :editImgSrc="showPicUrl" :cleanImgSrc="pagePicUrl" @getBlobData="getImgData"></draw-image>
    </div>
  </div>
</template>

<script>
import drawImage from '@/components/DrawImage'
export default {
  name: 'App',
  components: {
    drawImage
  },
  data() {
    return {
      showPicUrl: '',
      pagePicUrl: '',
      editPic: 'https://techimg.ziroom.com/0458ec80-6ae4-4f72-a74a-7bb89ccb705e',
      addPic: 'https://techimg.ziroom.com/f930fa53-67ea-4d91-bc02-dd1d8bf86688.jpg',
      clearPic: 'https://techimg.ziroom.com/f930fa53-67ea-4d91-bc02-dd1d8bf86688.jpg',
    }
  },
  mounted() {
    this.changePic()
  },
  methods: {
    startDraw() {
      this.$refs.drawImage.startDraw()
    },
    stopDraw() {
      this.$refs.drawImage.stopDraw()
    },
    clearDraw() {
      this.$refs.drawImage.initDraw()
    },
    // 选择页面位置
    async submitPage() {
      await this.$refs.drawImage.saveImageData();
      console.log('888888', this.cropperImg)
    },
    getImgData(data) {
      this.cropperImg = data
    },

    changePic() {
      this.pagePicUrl = this.clearPic
      this.showPicUrl = this.editPic
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
