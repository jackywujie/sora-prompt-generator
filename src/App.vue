<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-600 to-pink-600 p-8">
    <div class="max-w-6xl mx-auto">
      <div class="bg-white rounded-2xl shadow-2xl overflow-hidden">
        <!-- Header -->
        <div class="bg-gradient-to-r from-purple-700 to-pink-700 text-white p-8">
          <h1 class="text-4xl font-bold mb-2">🎬 Sora Prompt 生成器</h1>
          <p class="text-purple-100">专业级视频 Prompt 生成工具 | 基于 OpenAI Sora 2 官方标准</p>
        </div>

        <!-- Content -->
        <div class="p-8">
          <!-- Step Indicator -->
          <div class="mb-8">
            <div class="flex justify-between items-center mb-4">
              <span class="text-lg font-semibold text-gray-700">第 {{ currentStep }}/10 步</span>
              <span class="text-sm text-gray-500">{{ Math.round((currentStep / 10) * 100) }}% 完成</span>
            </div>
            <div class="w-full bg-gray-200 rounded-full h-3">
              <div 
                class="bg-gradient-to-r from-purple-600 to-pink-600 h-3 rounded-full transition-all duration-300"
                :style="{ width: (currentStep / 10 * 100) + '%' }"
              ></div>
            </div>
          </div>

          <!-- Form Grid -->
          <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            <!-- Left Panel -->
            <div class="lg:col-span-2">
              <!-- Step 1: Basic Info -->
              <div v-if="currentStep === 1" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">基础信息</h2>
                
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">视频时长</label>
                  <select v-model="formData.duration" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none">
                    <option value="">选择时长</option>
                    <option value="4">4 秒</option>
                    <option value="8">8 秒</option>
                    <option value="12">12 秒</option>
                  </select>
                </div>

                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Model 选择</label>
                  <select v-model="formData.model" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none">
                    <option value="">选择 Model</option>
                    <option value="sora-2">Sora 2</option>
                    <option value="sora-2-pro">Sora 2 Pro</option>
                  </select>
                </div>

                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">分辨率</label>
                  <select v-model="formData.resolution" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none">
                    <option value="">选择分辨率</option>
                    <option value="1280x720">1280x720</option>
                    <option value="720x1280">720x1280</option>
                  </select>
                </div>

                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">写法风格</label>
                  <div class="grid grid-cols-2 gap-4">
                    <button 
                      @click="formData.style = 'director'"
                      :class="['p-4 rounded-lg border-2 transition-all', formData.style === 'director' ? 'border-purple-600 bg-purple-50' : 'border-gray-300']"
                    >
                      🎬 导演级详细
                    </button>
                    <button 
                      @click="formData.style = 'simple'"
                      :class="['p-4 rounded-lg border-2 transition-all', formData.style === 'simple' ? 'border-pink-600 bg-pink-50' : 'border-gray-300']"
                    >
                      ✨ 简洁创意
                    </button>
                  </div>
                </div>
              </div>

              <!-- Step 2: Subject -->
              <div v-else-if="currentStep === 2" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">主体设定（Subject）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">主角/主体描述</label>
                  <textarea v-model="formData.subject" placeholder="例如：一位20岁的年轻女性，穿着红色连衣裙..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-24" />
                </div>
              </div>

              <!-- Step 3: Environment -->
              <div v-else-if="currentStep === 3" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">场景与环境（Environment）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">主场景描写</label>
                  <textarea v-model="formData.environment" placeholder="例如：江苏泰兴的农村小镇..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-24" />
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">时代背景</label>
                  <input v-model="formData.era" placeholder="例如：1987年初夏" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none" />
                </div>
              </div>

              <!-- Step 4: Cinematography -->
              <div v-else-if="currentStep === 4" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">镜头与摄影（Cinematography）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Camera Shot 类型</label>
                  <select v-model="formData.cameraShot" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none">
                    <option value="">选择镜头类型</option>
                    <option value="wide">Wide shot（广角）</option>
                    <option value="medium">Medium shot（中景）</option>
                    <option value="closeup">Close-up（特写）</option>
                  </select>
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Camera Movement</label>
                  <select v-model="formData.cameraMovement" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none">
                    <option value="">选择运动方式</option>
                    <option value="static">Static（静态）</option>
                    <option value="dolly">Dolly（推拉）</option>
                    <option value="pan">Pan（摇镜头）</option>
                  </select>
                </div>
              </div>

              <!-- Step 5: Lighting -->
              <div v-else-if="currentStep === 5" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">光线与色彩（Lighting & Color）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">光线描写</label>
                  <textarea v-model="formData.lighting" placeholder="例如：Golden hour light..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">主色调 1</label>
                  <input v-model="formData.color1" placeholder="例如：golden" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none" />
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">主色调 2</label>
                  <input v-model="formData.color2" placeholder="例如：warm" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none" />
                </div>
              </div>

              <!-- Step 6: Actions -->
              <div v-else-if="currentStep === 6" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">动作与运动（Action）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Action Beat 1</label>
                  <textarea v-model="formData.action1" placeholder="具体动作描写..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Action Beat 2</label>
                  <textarea v-model="formData.action2" placeholder="第二个动作..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
              </div>

              <!-- Step 7: Style -->
              <div v-else-if="currentStep === 7" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">视觉风格与氛围（Style）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">视觉风格</label>
                  <textarea v-model="formData.visualStyle" placeholder="例如：Film look、Documentary..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Mood/Atmosphere</label>
                  <textarea v-model="formData.mood" placeholder="例如：Cinematic、Warm..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
              </div>

              <!-- Step 8: Audio -->
              <div v-else-if="currentStep === 8" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">音频与对话（Audio）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">背景音乐风格</label>
                  <textarea v-model="formData.music" placeholder="例如：uplifting orchestral strings..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">对话内容</label>
                  <textarea v-model="formData.dialogue" placeholder="如果有对话，输入在这里..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
              </div>

              <!-- Step 9: Advanced -->
              <div v-else-if="currentStep === 9" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">高级指引（Advanced）</h2>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">观众应感受到的情感</label>
                  <textarea v-model="formData.emotion" placeholder="例如：温暖、怀旧、震撼..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">特殊要求</label>
                  <textarea v-model="formData.specialRequirements" placeholder="例如：特定物体出现..." class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-purple-600 focus:outline-none h-20" />
                </div>
              </div>

              <!-- Step 10: Export -->
              <div v-else-if="currentStep === 10" class="space-y-6">
                <h2 class="text-2xl font-bold text-gray-800">✅ 生成完成！</h2>
                <p class="text-gray-600">你的 Sora Prompt 已生成，点击下方按钮导出：</p>
                <div class="space-y-3">
                  <button 
                    @click="copyPrompt"
                    class="w-full bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-4 rounded-lg transition-colors"
                  >
                    📋 复制英文 Prompt
                  </button>
                  <button 
                    @click="downloadMarkdown"
                    class="w-full bg-pink-600 hover:bg-pink-700 text-white font-bold py-3 px-4 rounded-lg transition-colors"
                  >
                    📥 下载 Markdown
                  </button>
                  <button 
                    @click="resetForm"
                    class="w-full bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-4 rounded-lg transition-colors"
                  >
                    🔄 重新开始
                  </button>
                </div>
              </div>
            </div>

            <!-- Right Panel - Preview -->
            <div class="lg:col-span-1">
              <div class="sticky top-8 bg-gray-50 rounded-lg p-6 border-2 border-gray-200">
                <h3 class="text-lg font-bold text-gray-800 mb-4">📝 Prompt 预览</h3>
                <div class="text-xs text-gray-600 space-y-2 max-h-96 overflow-y-auto font-mono">
                  <div v-if="formData.duration"><strong>Duration:</strong> {{ formData.duration }}s</div>
                  <div v-if="formData.subject"><strong>Subject:</strong> {{ formData.subject }}</div>
                  <div v-if="formData.environment"><strong>Environment:</strong> {{ formData.environment }}</div>
                  <div v-if="formData.cameraShot"><strong>Camera:</strong> {{ formData.cameraShot }}</div>
                  <div v-if="formData.lighting"><strong>Lighting:</strong> {{ formData.lighting }}</div>
                  <div v-if="formData.mood"><strong>Mood:</strong> {{ formData.mood }}</div>
                  <p class="text-gray-400 mt-4">✨ 填写信息后实时预览</p>
                </div>
              </div>
            </div>
          </div>

          <!-- Navigation -->
          <div class="flex justify-between gap-4 mt-8 pt-8 border-t border-gray-200">
            <button 
              @click="previousStep"
              :disabled="currentStep === 1"
              :class="['flex-1 py-3 px-4 rounded-lg font-bold transition-colors', currentStep === 1 ? 'bg-gray-200 text-gray-400 cursor-not-allowed' : 'bg-gray-500 hover:bg-gray-600 text-white']"
            >
              ← 上一步
            </button>
            <button 
              @click="nextStep"
              :disabled="currentStep === 10"
              :class="['flex-1 py-3 px-4 rounded-lg font-bold transition-colors', currentStep === 10 ? 'bg-gray-200 text-gray-400 cursor-not-allowed' : 'bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-700 hover:to-pink-700 text-white']"
            >
              下一步 →
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const currentStep = ref(1)

const formData = ref({
  duration: '',
  model: '',
  resolution: '',
  style: '',
  subject: '',
  environment: '',
  era: '',
  cameraShot: '',
  cameraMovement: '',
  lighting: '',
  color1: '',
  color2: '',
  action1: '',
  action2: '',
  visualStyle: '',
  mood: '',
  music: '',
  dialogue: '',
  emotion: '',
  specialRequirements: ''
})

const nextStep = () => {
  if (currentStep.value < 10) currentStep.value++
}

const previousStep = () => {
  if (currentStep.value > 1) currentStep.value--
}

const generatePrompt = () => {
  let prompt = `A ${formData.value.style === 'director' ? 'highly cinematic' : ''} video scene`
  
  if (formData.value.subject) prompt += ` featuring ${formData.value.subject}`
  if (formData.value.environment) prompt += `. Setting: ${formData.value.environment}`
  if (formData.value.cameraShot) prompt += `. Camera: ${formData.value.cameraShot}`
  if (formData.value.lighting) prompt += `. Lighting: ${formData.value.lighting}`
  if (formData.value.action1) prompt += `. Action: ${formData.value.action1}`
  if (formData.value.mood) prompt += `. Mood: ${formData.value.mood}`
  if (formData.value.music) prompt += `. Music: ${formData.value.music}`
  
  return prompt
}

const copyPrompt = () => {
  const prompt = generatePrompt()
  navigator.clipboard.writeText(prompt)
  alert('✅ Prompt 已复制到剪贴板！')
}

const downloadMarkdown = () => {
  const prompt = generatePrompt()
  const markdown = `# Sora Prompt 生成结果\n\n## 英文 Prompt\n\n${prompt}\n\n## 参数\n- Duration: ${formData.value.duration}s\n- Model: ${formData.value.model}\n- Resolution: ${formData.value.resolution}`
  
  const blob = new Blob([markdown], { type: 'text/markdown' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = 'sora-prompt.md'
  a.click()
}

const resetForm = () => {
  formData.value = {
    duration: '',
    model: '',
    resolution: '',
    style: '',
    subject: '',
    environment: '',
    era: '',
    cameraShot: '',
    cameraMovement: '',
    lighting: '',
    color1: '',
    color2: '',
    action1: '',
    action2: '',
    visualStyle: '',
    mood: '',
    music: '',
    dialogue: '',
    emotion: '',
    specialRequirements: ''
  }
  currentStep.value = 1
}
</script>

<style scoped>
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.animate-fadeIn {
  animation: fadeIn 0.3s ease-in;
}
</style>
