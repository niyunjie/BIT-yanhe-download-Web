<script setup>
defineProps({
  user: { type: Object, default: null },
  backendInfo: { type: Object, required: true },
})

defineEmits(['logout'])
</script>

<template>
  <header class="topbar">
    <div>
      <p class="eyebrow">Workspace</p>
      <h1>课程工作台</h1>
      <p class="topbar-copy">检索课程、选择课次，然后播放、下载或导出课件。</p>
    </div>

    <div class="topbar-meta">
      <div class="topbar-chip">
        <span>当前用户</span>
        <strong>{{ user?.nickname || '已登录用户' }}</strong>
      </div>
      <div class="topbar-chip" :class="{ online: backendInfo.ok }">
        <span>服务状态</span>
        <strong>{{ backendInfo.ok ? '在线' : '离线' }}</strong>
      </div>
      <button class="ghost-button" @click="$emit('logout')">退出登录</button>
    </div>
  </header>
</template>
