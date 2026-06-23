<template>
  <n-config-provider>
    <n-message-provider>
      <div class="h-screen flex flex-col bg-gray-50">
        <!-- Top: Skill Tabs -->
        <n-layout-header class="bg-white shadow-sm border-b">
          <div class="px-6 py-3 flex items-center justify-between">
            <h1 class="text-xl font-bold text-gray-800">UI Skill Comparison</h1>
            <n-tabs 
              v-model:value="activeSkill" 
              type="line" 
              @update:value="handleSkillChange"
            >
              <n-tab-pane 
                v-for="skill in skills" 
                :key="skill.id"
                :name="skill.id"
                :tab="skill.name"
              >
              </n-tab-pane>
            </n-tabs>
          </div>
        </n-layout-header>

        <!-- Main Content -->
        <div class="flex flex-1 overflow-hidden">
          <!-- Left Sidebar: Example List -->
          <div class="w-64 bg-white border-r overflow-y-auto">
            <div class="p-4">
              <h3 class="text-sm font-semibold text-gray-500 uppercase tracking-wider mb-3">
                示例列表
              </h3>
              <n-menu
                :options="exampleOptions"
                :value="activeExample"
                @update:value="handleExampleChange"
                class="border-0"
              />
            </div>
          </div>

          <!-- Right Content: Prompt + Result -->
          <div class="flex-1 flex overflow-hidden">
            <!-- Left: Prompt Display -->
            <div class="w-1/2 border-r bg-white overflow-y-auto">
              <div class="p-6">
                <div class="flex items-center justify-between mb-4">
                  <h2 class="text-lg font-semibold text-gray-700">
                    提示词
                  </h2>
                  <n-tag type="info" size="small">
                    {{ activeSkill }}
                  </n-tag>
                </div>
                
                <n-card class="bg-gray-50">
                  <div class="prose max-w-none">
                    <pre class="whitespace-pre-wrap text-sm text-gray-700 font-mono bg-gray-100 p-4 rounded-lg">{{ currentPrompt }}</pre>
                  </div>
                </n-card>
              </div>
            </div>

            <!-- Right: Skill Result (iframe) -->
            <div class="w-1/2 bg-gray-100 overflow-hidden">
              <div class="p-6 h-full flex flex-col">
                <div class="flex items-center justify-between mb-4">
                  <h2 class="text-lg font-semibold text-gray-700">
                    生成结果
                  </h2>
                  <n-space>
                    <n-button 
                      size="small" 
                      @click="refreshIframe"
                      :loading="iframeLoading"
                    >
                      刷新
                    </n-button>
                    <n-button 
                      size="small" 
                      @click="openInNewTab"
                    >
                      新窗口打开
                    </n-button>
                  </n-space>
                </div>
                
                <div class="flex-1 bg-white rounded-lg shadow-sm overflow-hidden">
                  <iframe
                    v-if="currentResultUrl"
                    :src="currentResultUrl"
                    class="w-full h-full border-0"
                    @load="iframeLoading = false"
                  ></iframe>
                  <div 
                    v-else 
                    class="w-full h-full flex items-center justify-center text-gray-400"
                  >
                    请选择示例和技能查看结果
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </n-message-provider>
  </n-config-provider>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  NConfigProvider,
  NMessageProvider,
  NLayout,
  NLayoutHeader,
  NTabs,
  NTabPane,
  NSpace,
  NButton,
  NCard,
  NTag,
  NMenu,
  useMessage
} from 'naive-ui'

const message = useMessage()

// Skills data
const skills = ref([
  { id: 'frontend-design', name: 'Frontend Design', description: '前端设计技能' },
  { id: 'ui-ux-pro-max', name: 'UI/UX Pro Max', description: '专业UI/UX设计' },
  { id: 'taste', name: 'Taste Skill', description: '审美设计技能' }
])

const activeSkill = ref('frontend-design')

// Examples data
const examples = ref([
  {
    id: 'landing-page',
    name: '产品落地页',
    prompt: `设计一个现代化的 SaaS 产品落地页，包含：
- 吸引人的 Hero Section
- 产品特性展示（3-4个核心功能）
- 定价方案对比
- 用户评价/社交证明
- Footer 联系方式

要求：简洁、专业、有视觉冲击力`
  },
  {
    id: 'dashboard',
    name: '数据仪表盘',
    prompt: `设计一个数据分析仪表盘界面，包含：
- 顶部关键指标卡片（KPI）
- 图表展示区域（折线图、柱状图、饼图）
- 侧边栏导航
- 实时数据更新提示
- 深色/浅色主题切换

要求：信息密度高但不拥挤，数据可视化清晰`
  },
  {
    id: 'blog-layout',
    name: '博客文章页',
    prompt: `设计一个技术博客的文章页面，包含：
- 文章标题和元信息（作者、日期、阅读时间）
- 目录导航（TOC）
- 正文排版（代码高亮、引用块、图片）
- 评论区
- 相关文章推荐
- 移动端适配

要求：优秀的阅读体验，排版精美`
  }
])

const activeExample = ref('landing-page')

// Computed
const exampleOptions = computed(() => {
  return examples.value.map(example => ({
    key: example.id,
    label: example.name
  }))
})

const currentPrompt = computed(() => {
  const example = examples.value.find(e => e.id === activeExample.value)
  return example ? example.prompt : ''
})

const currentResultUrl = computed(() => {
  // 这里指向 public 目录下的 HTML 文件
  return `/results/${activeSkill.value}/${activeExample.value}.html`
})

const iframeLoading = ref(false)

// Methods
const handleSkillChange = (skillId: string) => {
  activeSkill.value = skillId
  iframeLoading.value = true
  message.info(`切换到 ${skills.value.find(s => s.id === skillId)?.name}`)
}

const handleExampleChange = (exampleId: string) => {
  activeExample.value = exampleId
  iframeLoading.value = true
  message.info(`加载示例: ${examples.value.find(e => e.id === exampleId)?.name}`)
}

const refreshIframe = () => {
  iframeLoading.value = true
  const iframe = document.querySelector('iframe')
  if (iframe) {
    iframe.src = iframe.src
  }
  message.success('已刷新')
}

const openInNewTab = () => {
  if (currentResultUrl.value) {
    window.open(currentResultUrl.value, '_blank')
  }
}
</script>
