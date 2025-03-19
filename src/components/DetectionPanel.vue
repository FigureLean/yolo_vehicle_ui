<!--<template>-->
<!--  <div class="detection-panel">-->
<!--    <div class="button-section">-->
<!--      <button @click="handleImageDetection">图像检测</button>-->
<!--      <button @click="handleVideoDetection">视频检测</button>-->
<!--      <button @click="handleCameraDetection">摄像头检测</button>-->
<!--    </div>-->

<!--    <div class="input-section">-->
<!--      <h2>输入</h2>-->
<!--      <input type="text" placeholder="选择文件" readonly v-model="selectedFile" />-->
<!--      <input type="file" @change="onFileChange" style="display: none;" ref="fileInput" />-->
<!--      <button @click="triggerFileInput">选择文件</button>-->
<!--      <div v-if="selectedFile">-->
<!--        <h3>预览:</h3>-->
<!--        <video v-if="isVideo" controls ref="videoElement">-->
<!--          <source :src="fileSrc" type="video/mp4">-->
<!--          您的浏览器不支持视频标签。-->
<!--        </video>-->
<!--        <img v-else :src="fileSrc" alt="Image Preview" />-->
<!--      </div>-->
<!--    </div>-->
<!--    <div class="output-section">-->
<!--      <h2>输出</h2>-->
<!--      <textarea rows="10" cols="50" readonly>{{ output }}</textarea>-->
<!--    </div>-->
<!--  </div>-->
<!--</template>-->

<!--<script>-->
<!--import axios from 'axios';-->

<!--export default {-->
<!--  name: 'DetectionPanel',-->
<!--  data() {-->
<!--    return {-->
<!--      output: '',-->
<!--      selectedFile: '',-->
<!--      fileSrc: null,-->
<!--      isVideo: false,-->
<!--      videoElement: null-->
<!--    };-->
<!--  },-->
<!--  methods: {-->
<!--    handleImageDetection() {-->
<!--      this.output = '图像检测结果';-->
<!--    },-->
<!--    handleVideoDetection() {-->
<!--      if (this.selectedFile && this.isVideo) {-->
<!--        this.output = '视频检测中...';-->
<!--        // 这里使用GET请求触发后端处理-->
<!--        axios.get('http://localhost:8000/detect', {-->
<!--          params: {-->
<!--            filename: this.selectedFile-->
<!--          }-->
<!--        })-->
<!--            .then(response => {-->
<!--              console.log('Response:', response);-->
<!--              this.output = JSON.stringify(response.data, null, 2);-->
<!--            })-->
<!--            .catch(error => {-->
<!--              console.error('Error:', error);-->
<!--            });-->
<!--      } else {-->
<!--        this.output = '请选择一个视频文件';-->
<!--      }-->
<!--    },-->


<!--    handleCameraDetection() {-->
<!--      this.output = '摄像头检测结果';-->
<!--    },-->
<!--    triggerFileInput() {-->
<!--      this.$refs.fileInput.click();-->
<!--    },-->
<!--    onFileChange(event) {-->
<!--      const file = event.target.files[0];-->
<!--      if (file) {-->
<!--        this.selectedFile = file.name;-->
<!--        this.fileSrc = URL.createObjectURL(file);-->
<!--        this.isVideo = file.type.startsWith('video/');-->
<!--      }-->
<!--    }-->
<!--  }-->
<!--};-->
<!--</script>-->

<!--<style scoped>-->
<!--.detection-panel {-->
<!--  display: flex;-->
<!--  flex-direction: column;-->
<!--  align-items: center;-->
<!--}-->
<!--.button-section {-->
<!--  margin: 10px;-->
<!--}-->
<!--.input-section, .output-section {-->
<!--  margin: 10px;-->
<!--  padding: 20px;-->
<!--  border: 1px solid #ccc;-->
<!--  border-radius: 5px;-->
<!--  width: 300px;-->
<!--}-->
<!--textarea {-->
<!--  width: 100%;-->
<!--  height: 150px;-->
<!--}-->
<!--video, img {-->
<!--  max-width: 100%;-->
<!--  height: auto;-->
<!--  margin-top: 10px;-->
<!--}-->
<!--</style>-->

<template>
  <div class="detection-panel">
    <div class="button-section">
      <button @click="handleImageDetection">图像检测</button>
      <button @click="handleVideoDetection">视频检测</button>
      <button @click="handleCameraDetection">摄像头检测</button>
    </div>

    <div class="input-section">
      <h2>输入</h2>
      <input type="text" placeholder="选择文件" readonly v-model="selectedFile" />
      <input type="file" @change="onFileChange" style="display: none;" ref="fileInput" />
      <button @click="triggerFileInput">选择文件</button>
      <div v-if="selectedFile">
        <h3>预览:</h3>
        <video v-if="isVideo" controls ref="videoElement">
          <source :src="fileSrc" type="video/mp4">
          您的浏览器不支持视频标签。
        </video>
        <img v-else :src="fileSrc" alt="Image Preview" />
      </div>
    </div>
    <div class="output-section">
      <h2>输出</h2>
      <textarea rows="10" cols="50" readonly>{{ output }}</textarea>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'DetectionPanel',
  data() {
    return {
      output: '',
      selectedFile: '',
      fileSrc: null,
      isVideo: false,
      videoElement: null
    };
  },
  methods: {
    handleImageDetection() {
      if (this.selectedFile && !this.isVideo) {
        this.output = '图像检测中...';
        axios.get('http://localhost:8000/detect', {
          params: {
            type: 'image',
            filename: this.selectedFile
          }
        })
            .then(response => {
              console.log('Response:', response);
              this.output = JSON.stringify(response.data, null, 2);
            })
            .catch(error => {
              console.error('Error:', error);
            });
      } else {
        this.output = '请选择一个图像文件';
      }
    },
    handleVideoDetection() {
      if (this.selectedFile && this.isVideo) {
        this.output = '视频检测中...';
        axios.get('http://localhost:8000/detect', {
          params: {
            type: 'video',
            filename: this.selectedFile
          }
        })
            .then(response => {
              console.log('Response:', response);
              this.output = JSON.stringify(response.data, null, 2);
            })
            .catch(error => {
              console.error('Error:', error);
            });
      } else {
        this.output = '请选择一个视频文件';
      }
    },
    handleCameraDetection() {
      this.output = '摄像头检测结果';
    },
    triggerFileInput() {
      this.$refs.fileInput.click();
    },
    onFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.selectedFile = file.name;
        this.fileSrc = URL.createObjectURL(file);
        this.isVideo = file.type.startsWith('video/');
      }
    }
  }
};
</script>

<style scoped>
.detection-panel {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.button-section {
  margin: 10px;
}
.input-section, .output-section {
  margin: 10px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  width: 300px;
}
textarea {
  width: 100%;
  height: 150px;
}
video, img {
  max-width: 100%;
  height: auto;
  margin-top: 10px;
}
</style>