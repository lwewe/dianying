<template>
  <div class="feedback-container">
    <!-- 反馈类型 -->
    <div class="feedback-type">
      <div>
        <img :src="dataList.img" alt="" class="lfimg"></img>
      </div>
      <div>
        <div class="type-title">{{dataList.title}}</div>
        <div class="type-desc">{{dataList.content}}</div>
      </div>
    </div>

    <!-- 问题与意见 -->
    <div class="feedback-content">
      <div class="section-title">问题与意见</div>
      <p class="hint-text">请详细描述您的问题，以便我们为您提供更好的帮助</p>

      <!-- 文本输入区 -->
      <textarea v-model="feedbackText" class="feedback-textarea" placeholder="请输入您的具体问题和建议..." maxlength="200"
        rows="5"></textarea>
      <div class="text-count">{{ feedbackText.length }}/200</div>

      <!-- 图片上传 -->
      <div class="upload-section">
        <div class="upload-title">
          提供相关的图片（选填）
          <span class="upload-limit">最多4张</span>
        </div>
        <p class="hint-text">上传【有效图片】可以让问题能更好的被解决</p>

        <div class="upload-images">
          <!-- 已上传的图片 -->
          <div v-for="(image, index) in uploadedImages" :key="index" class="image-item">
            <img :src="image.url" alt="反馈图片" />
            <div class="image-actions">
              <span class="delete-btn" @click="removeImage(index)">×</span>
            </div>
          </div>

          <!-- 上传按钮 -->
          <div v-if="uploadedImages.length < 4" class="upload-btn" @click="triggerFileInput">
            <div class="upload-icon">+</div>
            <div class="upload-text">上传图片</div>
            <input ref="fileInput" type="file" accept="image/*" multiple style="display: none"
              @change="handleImageUpload" />
          </div>
        </div>
      </div>
    </div>

    <!-- 提交按钮 -->
    <div class="submit-btn" :class="{ 'disabled': !canSubmit }" @click="submitFeedback">
      提交
    </div>
  </div>
</template>

<script>
import { Toast } from 'vant';
import { feedShow,feedUploadimg,addfeedBack } from "@/api/locality/order";
export default {
  name: 'FeedbackForm',
  data() {
    return {
      src1: require("../../assets/yj/1.png"),
      feedbackText: '',
      uploadedImages: [], // 存储上传的图片
      maxImages: 4,ids:'',dataList:""
    }
  },
  computed: {
    canSubmit() {
      return this.feedbackText.trim().length > 0
    }
  },
   created() {
    this.token = localStorage.getItem("token")
    this.ids = this.$route.query.id
    
  },
  mounted(){
    this.feedback();
  },
  methods: {
      feedback() {
      feedShow({
token:this.token,
id:this.ids||''
      }).then(res => {
         console.log(res)
         this.dataList = res.data.show;

      })
    },
    // 触发文件选择
    triggerFileInput() {
      this.$refs.fileInput.click()
    },

    // 处理图片上传
    handleImageUpload(event) {
      const files = event.target.files
      const remainingSlots = this.maxImages - this.uploadedImages.length

      if (files.length > remainingSlots) {
        Toast(`最多只能上传${this.maxImages}张图片`)
        return
      }

      // 处理每个文件
      for (let i = 0; i < Math.min(files.length, remainingSlots); i++) {
        const file = files[i]

        // 检查文件类型
        if (!file.type.startsWith('image/')) {
          Toast('请上传图片文件')
          continue
        }

        // 检查文件大小（限制为5MB）
        if (file.size > 5 * 1024 * 1024) {
          Toast('图片大小不能超过5MB')
          continue
        }

        // 预览图片
        this.previewImage(file)
      }

      // 重置文件输入
      event.target.value = ''
    },

    // 预览图片
    previewImage(file) {
      const reader = new FileReader()
      reader.onload = (e) => {
        this.uploadedImages.push({
          file: file,
          url: e.target.result
        })
      }
      reader.readAsDataURL(file)
    },

    // 移除图片
    removeImage(index) {
      this.uploadedImages.splice(index, 1)
    },

    // 提交反馈
    async submitFeedback() {
      if (!this.canSubmit) {
        Toast('请输入反馈内容')
        return
      }

      try {
        // 创建FormData对象
        const formData = new FormData()
        formData.append('feedback', this.feedbackText.trim())
        formData.append('type', 'service_quality')
        formData.append('timestamp', new Date().toISOString())

        // 添加图片
        this.uploadedImages.forEach((image, index) => {
          formData.append(`images[${index}]`, image.file)
        })

        // 这里调用实际的API
        // const response = await submitFeedback(formData)

        // 模拟提交成功
        // Toast.success('提交成功！\n感谢您的建议')

        Toast.success({
          message: '提交成功！\n感谢您的建议',
          className: 'my-toast-center',
          duration: 1500 // 显示时长
        })

        setTimeout(() => {
          this.$router.push({ path: 'feedback' })
        }, 1500)
        // 重置表单
        this.resetForm()

      } catch (error) {
        console.error('提交失败:', error)
        Toast.fail('提交失败，请稍后重试')
      }
    },

    // 重置表单
    resetForm() {
      this.feedbackText = ''
      this.uploadedImages = []
    }
  }
}
</script>
<style lang="less">
.my-toast-center {
  text-align: center !important;

  .van-toast__text {
    text-align: center !important;
    display: block !important;
    width: 100% !important;
  }
}
</style>
<style scoped lang="less">
.feedback-container {
  padding: 16px;
  background: #f5f5f5;
  min-height: 100vh;
}

.feedback-type {
  display: flex;
  background: white;
  border-radius: 8px;
  padding: 14px;
  margin-bottom: 12px;

  .type-title {
    font-size: 14px;
    font-weight: bold;
    color: #333;
    margin-bottom: 8px;
  }

  .type-desc {
    font-size: 12px;
    color: #666;
    line-height: 1.4;
  }
}

.feedback-content {
  background: white;
  border-radius: 8px;
  padding: 14px;
  margin-bottom: 12px;
}

.section-title {
  font-size: 14px;
  font-weight: bold;
  color: #333;
  margin-bottom: 8px;
}

.hint-text {
  font-size: 12px;
  color: #999;
  margin-bottom: 12px;
  line-height: 1.4;
}

.feedback-textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  font-size: 12px;
  resize: vertical;
  box-sizing: border-box;

  &:focus {
    outline: none;
    border-color: #409eff;
  }
}

.text-count {
  text-align: right;
  font-size: 12px;
  color: #999;
  margin-top: 4px;
}

.upload-section {
  margin-top: 20px;
}

.upload-title {
  font-size: 14px;
  font-weight: bold;
  color: #333;
  margin-bottom: 8px;
  display: flex;
  justify-content: space-between;
  align-items: center;

  .upload-limit {
    font-size: 12px;
    color: #999;
    font-weight: normal;
  }
}

.upload-images {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-top: 12px;
}

.lfimg {
  width: 40px;
  margin-right: 10px;
}

.image-item {
  width: 73px;
  height: 73px;
  border-radius: 6px;
  overflow: hidden;
  position: relative;

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .image-actions {
    position: absolute;
    top: 2px;
    right: 2px;

    .delete-btn {
      width: 20px;
      height: 20px;
      background: rgba(0, 0, 0, 0.7);
      color: white;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 16px;
      cursor: pointer;

      &:hover {
        background: rgba(0, 0, 0, 0.9);
      }
    }
  }
}

.upload-btn {
  width: 80px;
  height: 80px;
  border: 1px dashed #dcdfe6;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s;

  &:hover {
    border-color: #409eff;
    color: #409eff;
  }

  .upload-icon {
    font-size: 24px;
    color: #999;
    margin-bottom: 4px;
  }

  .upload-text {
    font-size: 12px;
    color: #999;
  }
}

.submit-btn {
  background: linear-gradient(to bottom, #4896FF, #4896FF);
  color: white;
  font-size: 14px;
  text-align: center;
  padding: 14px;
  border-radius: 8px;
  margin-top: 20px;
  cursor: pointer;
  transition: opacity 0.3s;

  &:hover {
    opacity: 0.9;
  }

  &.disabled {
    background: #ccc;
    cursor: not-allowed;

    &:hover {
      opacity: 1;
    }
  }
}

// 响应式设计
@media (max-width: 375px) {

  .image-item,
  .upload-btn {
    width: 70px;
    height: 70px;
  }
}
</style>