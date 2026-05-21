<script setup>
const streamType = defineModel('streamType', { type: String, default: 'main' })

defineProps({
  selectedCourse: { type: Object, default: null },
  selectedSession: { type: Object, default: null },
  selectedSessionIds: { type: Array, default: () => [] },
  selectedCount: { type: Number, default: 0 },
  isLoadingSessions: { type: Boolean, default: false },
  sessionError: { type: String, default: '' },
  isProcessingQueue: { type: Boolean, default: false },
  formatSessionTime: { type: Function, required: true },
})

defineEmits(['toggleSelectAllSessions', 'playSelectedSession', 'enqueueDownloads', 'enqueueSlideExtraction', 'toggleSession'])
</script>

<template>
  <section class="panel session-actions-panel">
    <div class="session-command-bar">
      <div class="stream-segment">
        <span>视频流</span>
        <div class="segmented-control" role="group" aria-label="视频流选择">
          <button type="button" :class="{ active: streamType === 'main' }" @click="streamType = 'main'">
            主视频
          </button>
          <button type="button" :class="{ active: streamType === 'vga' }" @click="streamType = 'vga'">
            屏幕流
          </button>
        </div>
      </div>

      <div class="mode-actions">
        <button class="ghost-button" @click="$emit('toggleSelectAllSessions')">
          {{ selectedCourse && selectedCount === selectedCourse.sessions.length ? '取消全选' : '全选' }}
        </button>
        <button class="ghost-button" :disabled="!selectedSession" @click="$emit('playSelectedSession')">
          在线播放
        </button>
        <button class="primary-button" :disabled="selectedCount === 0 || isProcessingQueue" @click="$emit('enqueueDownloads')">
          下载 TS
        </button>
        <button class="ghost-button" :disabled="!selectedSession || streamType !== 'vga' || isProcessingQueue" @click="$emit('enqueueSlideExtraction')">
          导出课件 PDF
        </button>
      </div>
    </div>

    <p class="panel-tip">课件提取仅对屏幕流有效；在线播放和课件导出需要只选择一个课次。</p>

    <div v-if="selectedCourse" class="session-list">
      <label v-for="session in selectedCourse.sessions" :key="session.sessionId" class="session-card">
        <input
          type="checkbox"
          :checked="selectedSessionIds.includes(String(session.sessionId))"
          @change="$emit('toggleSession', session.sessionId)"
        />
        <div>
          <strong>{{ session.title }}</strong>
          <p>{{ formatSessionTime(session) }}</p>
          <p>主视频 {{ session.mainUrl ? '有' : '无' }} / 屏幕流 {{ session.vgaUrl ? '有' : '无' }}</p>
        </div>
      </label>
    </div>

    <div v-else class="empty-state">
      <p v-if="isLoadingSessions">正在加载课次...</p>
      <p v-else>请先选择一门课程。</p>
    </div>

    <p v-if="sessionError" class="error-text">{{ sessionError }}</p>
  </section>
</template>
