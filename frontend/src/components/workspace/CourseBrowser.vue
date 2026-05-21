<script setup>
defineProps({
  filterState: { type: Object, required: true },
  semesters: { type: Array, default: () => [] },
  courses: { type: Array, default: () => [] },
  totalCourses: { type: Number, default: 0 },
  totalPages: { type: Number, default: 1 },
  pageSize: { type: Number, default: 12 },
  canSearch: { type: Boolean, default: false },
  isLoadingCourses: { type: Boolean, default: false },
  courseError: { type: String, default: '' },
  selectedCourse: { type: Object, default: null },
  formatCourseProfessors: { type: Function, required: true },
  formatSemesterLabel: { type: Function, required: true },
})

defineEmits(['fetchCourses', 'resetFilters', 'loadCourseSessions'])
</script>

<template>
  <section class="panel course-browser-panel">
    <div class="section-head">
      <div>
        <p class="section-kicker">课程目录</p>
        <h2>选择课程</h2>
      </div>
      <span class="section-note">{{ totalCourses }} 门课程 · 每页 {{ pageSize }} 门</span>
    </div>

    <div class="course-search-band">
      <input
        v-model="filterState.keyword"
        type="text"
        class="search-input"
        placeholder="搜索课程名称"
        :disabled="!canSearch"
        @keyup.enter="$emit('fetchCourses', 1)"
      />
      <select v-model="filterState.scope" :disabled="!canSearch">
        <option value="all">全部课程</option>
        <option value="mine">我的课程</option>
      </select>
      <button class="primary-button" :disabled="!canSearch || isLoadingCourses" @click="$emit('fetchCourses', 1)">
        {{ isLoadingCourses ? '查询中...' : '查询' }}
      </button>
      <button class="ghost-button" :disabled="!canSearch || isLoadingCourses" @click="$emit('resetFilters')">
        重置
      </button>
    </div>

    <div class="semester-list">
      <label v-for="semester in semesters" :key="semester.id" class="chip">
        <input v-model="filterState.semesters" type="checkbox" :value="semester.id" :disabled="!canSearch" />
        <span>{{ semester.label }}</span>
      </label>
    </div>

    <p v-if="courseError" class="error-text">{{ courseError }}</p>

    <div class="course-grid">
      <button
        v-for="course in courses"
        :key="course.id"
        class="course-card"
        :class="{ active: selectedCourse?.courseId === course.id }"
        @click="$emit('loadCourseSessions', course)"
      >
        <strong class="course-name">{{ course.title }}</strong>
        <span>{{ formatCourseProfessors(course) }}</span>
        <span>{{ formatSemesterLabel(course) || '学期信息未提供' }}</span>
        <span>{{ course.collegeName || '院系信息未提供' }}</span>
      </button>
    </div>

    <div class="pagination">
      <button class="ghost-button" :disabled="filterState.page <= 1 || isLoadingCourses" @click="$emit('fetchCourses', filterState.page - 1)">
        上一页
      </button>
      <span>第 {{ filterState.page }} / {{ totalPages }} 页</span>
      <button class="ghost-button" :disabled="filterState.page >= totalPages || isLoadingCourses" @click="$emit('fetchCourses', filterState.page + 1)">
        下一页
      </button>
    </div>
  </section>
</template>
