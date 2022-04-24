<template>
  <div id="app">
    <div class="drawbox">
      <div>
        <button @click="startDraw">startDraw</button>
        <button @click="stopDraw">stopDraw</button>
        <button @click="clearDraw">clearDraw</button>
        <button @click="submitblob">save blob</button>
        <button @click="submitbase64">save base64</button>
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
      editPic: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fhbimg.huabanimg.com%2F9797222901bed0d659831893c259d77293a96b05568cd-DVB2A2_fw658&refer=http%3A%2F%2Fhbimg.huabanimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1653372629&t=2bdd272fdf9376b1a302f2a9465c27f5',
      clearPic: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fhbimg.huabanimg.com%2F9797222901bed0d659831893c259d77293a96b05568cd-DVB2A2_fw658&refer=http%3A%2F%2Fhbimg.huabanimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1653372629&t=2bdd272fdf9376b1a302f2a9465c27f5',
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
