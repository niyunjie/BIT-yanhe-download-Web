<script setup>
const username = defineModel('username', { type: String, default: '' })
const password = defineModel('password', { type: String, default: '' })
const manualToken = defineModel('manualToken', { type: String, default: '' })

defineProps({
  backendInfo: { type: Object, required: true },
  isBootstrapping: { type: Boolean, default: false },
  isLoggingIn: { type: Boolean, default: false },
  isCheckingToken: { type: Boolean, default: false },
  isSubmittingManualToken: { type: Boolean, default: false },
  loginError: { type: String, default: '' },
})

defineEmits(['login', 'submitManualToken'])
</script>

<template>
  <section class="auth-shell">
    <div class="auth-brand">
      <span class="auth-mark">Yanhe Workspace</span>
      <h1>课程下载与课件提取</h1>
      <p class="auth-copy">登录后可以检索课程、在线播放、下载 TS 视频，并从屏幕流导出课件 PDF。</p>

      <div class="auth-points">
        <div class="auth-point">
          <strong>在线播放</strong>
          <span>选择单个课次后直接播放，无需跳转。</span>
        </div>
        <div class="auth-point">
          <strong>本地下载</strong>
          <span>支持主视频和屏幕流在浏览器端保存。</span>
        </div>
        <div class="auth-point">
          <strong>课件导出</strong>
          <span>从屏幕流中提取页面变化并生成 PDF。</span>
        </div>
      </div>
    </div>

    <section class="auth-card">
      <div class="auth-card-head">
        <div>
          <p class="eyebrow">登录</p>
          <h2>进入工作台</h2>
        </div>
        <span class="backend-indicator" :class="{ online: backendInfo.ok }">
          {{ backendInfo.ok ? '服务在线' : '服务离线' }}
        </span>
      </div>

      <div v-if="isBootstrapping" class="auth-status">正在恢复登录状态...</div>

      <form class="login-form" @submit.prevent="$emit('login')">
        <label>
          <span>统一身份账号</span>
          <input v-model="username" type="text" placeholder="请输入学号或工号" />
        </label>
        <label>
          <span>统一身份密码</span>
          <input v-model="password" type="password" placeholder="请输入密码" />
        </label>
        <button class="primary-button auth-submit" :disabled="isLoggingIn || isCheckingToken">
          {{ isLoggingIn ? '登录中...' : isCheckingToken ? '验证中...' : '登录' }}
        </button>
      </form>

      <details class="token-panel">
        <summary>使用延河令牌登录</summary>
        <div class="manual-token-block">
          <label>
            <span>延河 Token</span>
            <input v-model="manualToken" type="text" placeholder="粘贴可用令牌" />
          </label>
          <button class="ghost-button" :disabled="isSubmittingManualToken || isCheckingToken" @click="$emit('submitManualToken')">
            {{ isSubmittingManualToken ? '验证中...' : '使用令牌登录' }}
          </button>
        </div>
      </details>

      <p v-if="loginError" class="error-text auth-error">{{ loginError }}</p>
    </section>
  </section>
</template>
