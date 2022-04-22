<template>
  <div id="app">
    <div class="drawbox">
      <div>
        <button @click="startDraw">框选位置</button>
        <button @click="stopDraw">结束框选</button>
        <button @click="clearDraw">清除框选</button>
        <button @click="submitblob">保 存 blob</button>
        <button @click="submitbase64">保 存 base64</button>
      </div>
      <div style="width: 500px; height: 800px;">
        <draw-image ref="drawImage" :editImgSrc="showPicUrl" :cleanImgSrc="pagePicUrl"></draw-image>
      </div>
    </div>

    <div class="imgbox" v-if="resBlob">
      <h3>resBlob</h3>
      <img :src="resBlob" style="width:100%; height:100%" />
    </div>
    <div class="imgbox" v-if="resBase64">
      <h3>resBase64</h3>
      <img :src="resBase64" style="width:100%; height:100%" />
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
      resBlob: '',
      resBase64: ''
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
    submitblob() {
      let blobData = this.$refs.drawImage.getBlobData();
      this.resBlob = blobData instanceof Blob ? URL.createObjectURL(blobData) : blobData;
    },
    submitbase64() {
      let base64Data = this.$refs.drawImage.getBase64Data();
      this.resBase64 = base64Data
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
  display: flex;
}
.drawbox {
  margin: auto 200px auto 20px;
}
button {
  font-size: 12px;
  border-radius: 3px;
  padding: 7px 15px;
  display: inline-block;
  line-height: 1;
  white-space: nowrap;
  cursor: pointer;
  background: #fff;
  border: 1px solid #dcdfe6;
  color: #606266;
  margin-right: 15px;
  margin-bottom: 10px;
}
.imgbox {
  width: 380px;
  margin-right: 50px;
  display: inline-block;
}
</style>
