<template>
  <div v-if="!verified" class="password-overlay">
    <div class="password-box">
      <h2>请输入访问密码</h2>
        <input 
          type="password" 
          v-model="inputPassword"
          placeholder="输入密码..."
          @keyup.enter="checkPassword"
        />
      <button @click="checkPassword">确认</button>
      <p v-if="error" class="error">{{ error }}</p>
    </div>
  </div>

  <Header title="骤雨重山图床" desc="拒绝流量劫持，全面使用HTTPS" />
  <main><RouterView /></main>
  <Footer />
  <Toaster />
</template>
<script setup lang="ts">
import Header from '@/components/Header/Header.vue';
import Footer from '@/components/Footer/Footer.vue';
import { Toaster } from '@/components/ui/toast';
</script>
<script>
export default {
  name: 'Home',
  data() {
    return {
      verified: false,          // 验证状态
      inputPassword: '',        // 用户输入的密码
      error: '',                // 错误信息
      correctPassword: '12345' // 硬编码的正确密码（实际使用请更换）
    }
  },
  mounted() {
    // 检查本地存储是否已有验证
    const saved = localStorage.getItem('passwordVerified')
    if (saved === 'true') {
      this.verified = true
    }
  },
  methods: {
    checkPassword() {
      if (this.inputPassword === this.correctPassword) {
        this.verified = true
        this.error = ''
        // 存储验证状态（24小时内有效）
        localStorage.setItem('passwordVerified', 'true')
        setTimeout(() => {
          localStorage.removeItem('passwordVerified')
        }, 24 * 60 * 60 * 1000) // 24小时后过期
      } else {
        this.error = '密码错误，请重新输入'
        this.inputPassword = ''
      }
    }
  }
}
</script>

<style scoped>
/* 遮罩层样式 */
.password-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

/* 密码框样式 */
.password-box {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  text-align: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.password-box input {
  padding: 10px;
  margin: 15px 0;
  width: 80%;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.password-box button {
  padding: 10px 20px;
  background: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background 0.3s;
}

.password-box button:hover {
  background: #359268;
}

/* 内容模糊效果 */
.blur-content {
  filter: blur(5px);
  pointer-events: none; /* 禁止交互 */
  user-select: none;    /* 禁止选择文本 */
}

.error {
  color: #ff4757;
  margin-top: 10px;
}
</style>
