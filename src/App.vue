<template>
  <n-config-provider>
    <n-message-provider>
      <div class="min-h-screen bg-gray-50">
        <!-- Header -->
        <n-layout-header class="bg-white shadow-sm">
          <div class="container mx-auto px-4 py-4 flex items-center justify-between">
            <h1 class="text-2xl font-bold text-gray-800">UI Skill Comparison</h1>
            <n-space>
              <n-button type="primary">GitHub</n-button>
            </n-space>
          </div>
        </n-layout-header>

        <!-- Main Content -->
        <n-layout class="mt-6">
          <n-layout-content class="container mx-auto px-4">
            <!-- Skill Showcase Cards -->
            <n-grid :x-gap="16" :y-gap="16" :cols="3">
              <n-gi v-for="skill in skills" :key="skill.name">
                <n-card 
                  :title="skill.name"
                  class="hover:shadow-lg transition-shadow cursor-pointer"
                  @click="handleSkillClick(skill)"
                >
                  <template #header-extra>
                    <n-tag :type="skill.status === 'active' ? 'success' : 'default'">
                      {{ skill.status === 'active' ? '可用' : '开发中' }}
                    </n-tag>
                  </template>
                  
                  <p class="text-gray-600 mb-4">{{ skill.description }}</p>
                  
                  <n-space vertical class="w-full">
                    <n-progress 
                      type="line" 
                      :percentage="skill.score" 
                      :show-indicator="false"
                    />
                    <div class="flex justify-between text-sm text-gray-500">
                      <span>设计评分</span>
                      <span>{{ skill.score }}分</span>
                    </div>
                  </n-space>
                </n-card>
              </n-gi>
            </n-grid>

            <!-- Demo Section -->
            <n-card title="设计技能演示区" class="mt-8">
              <n-space vertical size="large" class="w-full">
                <n-alert type="info" title="使用说明">
                  点击上方卡片查看不同设计技能的实际效果对比
                </n-alert>
                
                <n-divider />
                
                <div class="grid grid-cols-2 gap-4">
                  <n-button @click="message.info('这是 NaiveUI 按钮')" block>
                    NaiveUI 按钮示例
                  </n-button>
                  
                  <button 
                    class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded transition-colors"
                    @click="message.success('这是 TailwindCSS 按钮')"
                  >
                    TailwindCSS 按钮示例
                  </button>
                </div>
              </n-space>
            </n-card>
          </n-layout-content>
        </n-layout>
      </div>
    </n-message-provider>
  </n-config-provider>
</template>

<script setup lang="ts">
import { h } from 'vue'
import {
  NConfigProvider,
  NMessageProvider,
  NLayout,
  NLayoutHeader,
  NLayoutContent,
  NSpace,
  NButton,
  NCard,
  NGi,
  NGrid,
  NTag,
  NProgress,
  NAlert,
  NDivider,
  useMessage
} from 'naive-ui'

const message = useMessage()

interface Skill {
  name: string
  description: string
  score: number
  status: 'active' | 'development'
}

const skills: Skill[] = [
  {
    name: 'frontend-designer',
    description: '前端设计技能，注重交互体验和视觉效果',
    score: 85,
    status: 'active'
  },
  {
    name: 'ui-ux-pro-max',
    description: '专业 UI/UX 设计，提供极致用户体验',
    score: 92,
    status: 'active'
  },
  {
    name: 'taste-skill',
    description: '审美设计技能，提升整体视觉品味',
    score: 78,
    status: 'development'
  }
]

const handleSkillClick = (skill: Skill) => {
  message.info(`正在查看 ${skill.name} 的详细信息`)
}
</script>
