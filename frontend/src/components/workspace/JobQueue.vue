<script setup>
defineProps({
  browserJobs: { type: Array, default: () => [] },
  isProcessingQueue: { type: Boolean, default: false },
  jobStatusLabel: { type: Function, required: true },
  jobSummary: { type: Function, required: true },
})

defineEmits(['removeJob'])
</script>

<template>
  <section class="panel job-queue-panel">
    <div class="section-head">
      <div>
        <p class="section-kicker">任务队列</p>
        <h2>浏览器任务</h2>
      </div>
      <span class="section-note">{{ isProcessingQueue ? '执行中' : '空闲' }}</span>
    </div>

    <div v-if="browserJobs.length === 0" class="empty-state">
      <p>还没有任务。</p>
    </div>

    <div v-else class="task-list">
      <article v-for="job in browserJobs" :key="job.id" class="task-card">
        <div class="task-topline">
          <div>
            <h3>{{ job.sessionTitle }}</h3>
            <p>{{ job.courseTitle }}</p>
          </div>
          <span class="status-pill" :data-status="job.status.toLowerCase()">
            {{ jobStatusLabel(job) }}
          </span>
        </div>

        <div class="progress-track">
          <div class="progress-fill" :style="{ width: `${job.progress}%` }"></div>
        </div>

        <p class="task-meta">{{ jobSummary(job) }}</p>
        <p v-if="job.detail" class="task-meta">{{ job.detail }}</p>
        <p v-if="job.fileName" class="task-meta"><code>{{ job.fileName }}</code></p>
        <p v-if="job.error" class="error-text">{{ job.error }}</p>

        <button v-if="job.status === 'Completed' || job.status === 'Failed'" class="ghost-button" @click="$emit('removeJob', job.id)">
          移除
        </button>
      </article>
    </div>
  </section>
</template>
