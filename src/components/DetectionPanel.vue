<template>
  <div class="detection-panel">
    <div class="button-section">
      <button @click="handleImageDetection">图像检测</button>
      <button @click="handleVideoDetection">视频检测</button>
    </div>

    <div class="input-section">
      <h2>输入</h2>
      <input type="text" placeholder="选择文件" readonly v-model="selectedFile" />
      <input type="file" @change="onFileChange" style="display: none;" ref="fileInput" />
      <button @click="triggerFileInput">选择文件</button>

      <div v-if="selectedFile">
        <h3>原始预览:</h3>
        <video v-if="isVideo" controls>
          <source :src="fileSrc" type="video/mp4">
          您的浏览器不支持视频标签。
        </video>
        <img v-else :src="fileSrc" alt="原始图片" />
      </div>
    </div>

    <div class="output-section">
      <h2>检测结果</h2>
      <p>{{ output }}</p>
      <div v-if="resultSrc">
        <h3>检测后结果:</h3>
        <video v-if="isVideo" controls>
          <source :src="resultSrc" type="video/mp4">
        </video>
        <img v-else :src="resultSrc" alt="检测后图片" />
      </div>
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
      resultSrc: null,
      isVideo: false
    };
  },
  methods: {
    async uploadAndDetect(type) {
      if (!this.selectedFile) {
        this.output = "请选择文件";
        return;
      }

      this.output = "文件上传中...";
      let formData = new FormData();
      formData.append("file", this.$refs.fileInput.files[0]);

      try {
        // 上传文件到后端
        const uploadResponse = await axios.post("http://localhost:8000/upload/", formData, {
          headers: { "Content-Type": "multipart/form-data" }
        });

        if (uploadResponse.data.file_path) {
          // 文件上传成功，开始检测
          this.output = "文件上传成功，开始检测...";

          // 调用检测接口
          const detectResponse = await axios.get("http://localhost:8000/detect", {
            params: {
              type: type,
              filename: uploadResponse.data.file_path  // 使用上传文件的路径进行检测
            }
          });

          // // 处理检测结果
          // if (detectResponse.data.result_path) {
          //   // this.resultSrc = `http://localhost:8000/results/${detectResponse.data.result_path.split('/').pop()}`;
          //   // 修改前端拼接路径
          //   this.resultSrc = `http://localhost:8000/results/${detectResponse.data.result_path.split('/').pop()}`;
          //   this.output = "检测完成，显示结果";
          // } else {
          //   this.output = "未找到检测结果";
          // }
          if (detectResponse.data.result_path) {
            this.resultSrc = `http://localhost:8000/results/${detectResponse.data.result_path.split('/').pop()}`;
            this.output = "检测完成，显示结果";
          } else {
            this.output = "未找到检测结果";
          }

        } else {
          this.output = "文件上传失败，请重试";
        }

      } catch (error) {
        console.error("Error:", error);
        this.output = "发生错误：" + (error.response?.data?.detail || error.message);
      }
    },

    handleImageDetection() {
      this.uploadAndDetect("image");
    },
    handleVideoDetection() {
      this.uploadAndDetect("video");
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
