<template>
  <div class="file-element-wrapper">
    <div class="header">
      <i class="el-icon-document file-icon"></i>
      <div class="file-element">
        <span class="file-name">{{ fileName }}</span>
        <span class="file-size">{{ size }}</span>
      </div>
    </div>
    <div class="footer">
      <span @click="downloadFile" class="download-link">下载</span>
    </div>
    <el-progress
      v-if="showProgressBar"
      :percentage="percentage"
      :color="percentage => (percentage === 100 ? '#67c23a' : '#409eff')"
    />
  </div>
</template>

<script>
import { Progress } from 'element-ui'
export default {
  name: 'FileElement',
  props: {
    payload: {
      type: Object,
      required: true
    }
  },
  components: {
    ElProgress: Progress
  },
  computed: {
    fileName() {
      return this.payload.fileName
    },
    fileUrl() {
      return this.payload.fileUrl
    },
    size() {
      const size = this.payload.fileSize
      if (size > 1024) {
        if (size / 1024 > 1024) {
          return `${this.toFixed(size / 1024 / 1024)} Mb`
        }
        return `${this.toFixed(size / 1024)} Kb`
      }
      return `${this.toFixed(size)}B`
    },
    showProgressBar() {
      return this.$parent.message.status === 'unSend'
    },
    percentage() {
      return Math.floor((this.$parent.message.progress || 0) * 100)
    }
  },
  methods: {
    toFixed(number, precision = 2) {
      return number.toFixed(precision)
    },
    downloadFile() {
      // 浏览器支持fetch则用blob下载，避免浏览器点击a标签，跳转到新页面预览的行为
      if (window.fetch) {
        fetch(this.fileUrl)
          .then(res => res.blob())
          .then(blob => {
            let a = document.createElement('a')
            let url = window.URL.createObjectURL(blob)
            a.href = url
            a.download = this.fileName
            a.click()
          })
      } else {
        let a = document.createElement('a')
        a.href = this.fileUrl
        a.target = '_blank'
        a.download = this.filename
        a.click()
      }
    }
  }
}
</script>

<style scoped>
.file-element-wrapper {
  background-color: #fff;
  padding: 12px;
}
.header {
  display: flex;
}
.footer {
  border-top: 1px solid #dddddd;
  text-align: right;
  padding: 3px;
  margin-top: 8px;
}
.file-icon {
  font-size: 40px !important;
}
.file-element {
  display: flex;
  flex-direction: column;
  margin-left: 12px;
}
.file-size {
  font-size: 1.4rem;
  color: gray;
}
.download-link {
  cursor: pointer;
  color: #409eff;
}
</style>
