<script setup lang="ts">
/**
 * 学生工具集页面
 * 提供学习相关的实用工具和网站推荐
 */

import { NButton, NCard, NTag } from 'naive-ui';
import SvgIcon from '@/components/custom/svg-icon.vue';

defineOptions({
  name: 'StudentTools'
});

// 学习工具和网站列表数据
interface ToolItem {
  id: string;
  name: string;
  icon: string;
  localIcon?: string;
  description: string;
  url: string;
  type: string;
  color: string;
  bgColor: string;
}

// 学习网页数据（保持原有内容）
const webTools: ToolItem[] = [
  {
    id: 'baidu',
    name: '百度',
    icon: 'mdi:web',
    description: '国内最大的搜索引擎，提供全面的信息检索服务',
    url: 'https://www.baidu.com',
    type: '搜索引擎',
    color: '#4CAF50',
    bgColor: 'rgba(76, 175, 80, 0.1)'
  },
  {
    id: 'zhihu',
    name: '知乎',
    icon: 'mdi:message-reply-text-outline',
    description: '高质量问答社区，获取专业知识和经验分享',
    url: 'https://www.zhihu.com',
    type: '问答社区',
    color: '#0084FF',
    bgColor: 'rgba(0, 132, 255, 0.1)'
  },
  {
    id: 'github',
    name: 'GitHub',
    icon: 'mdi:github',
    description: '全球最大的代码托管平台，学习编程和开源项目',
    url: 'https://github.com',
    type: '代码平台',
    color: '#333333',
    bgColor: 'rgba(51, 51, 51, 0.1)'
  },
  {
    id: 'leetcode',
    name: 'LeetCode',
    icon: 'mdi:code-json',
    description: '编程算法练习平台，提升编程能力',
    url: 'https://leetcode.cn',
    type: '编程练习',
    color: '#FFA116',
    bgColor: 'rgba(255, 161, 22, 0.1)'
  },
  {
    id: 'csdn',
    name: 'CSDN',
    icon: 'mdi:book-open-variant',
    description: '中国最大的IT技术社区，提供技术文章和学习资源',
    url: 'https://www.csdn.net',
    type: '技术社区',
    color: '#F05A28',
    bgColor: 'rgba(240, 90, 40, 0.1)'
  },
  {
    id: 'notion',
    name: 'Notion',
    icon: 'mdi:notebook-outline',
    description: '多功能笔记工具，支持文档协作和知识管理',
    url: 'https://www.notion.so',
    type: '笔记工具',
    color: '#2E8B57',
    bgColor: 'rgba(46, 139, 87, 0.1)'
  }
];

// AI工具列表数据（新增的AI相关工具）
const aiTools: ToolItem[] = [
  {
    id: 'chatgpt',
    name: 'ChatGPT',
    icon: 'mdi:robot-outline',
    description: 'OpenAI开发的先进AI语言模型，支持多轮对话和内容生成',
    url: 'https://chat.openai.com',
    type: 'AI大模型',
    color: '#10A37F',
    bgColor: 'rgba(16, 163, 127, 0.1)'
  },
  {
    id: 'gemini',
    name: 'Gemini',
    icon: 'mdi:google',
    description: 'Google开发的多模态AI模型，支持文本、图像、音频等多种输入',
    url: 'https://gemini.google.com',
    type: 'AI大模型',
    color: '#4285F4',
    bgColor: 'rgba(66, 133, 244, 0.1)'
  },
  {
    id: 'claude',
    name: 'Claude',
    icon: 'mdi:brain',
    description: 'Anthropic开发的AI助手，以长文本处理和安全性著称',
    url: 'https://claude.ai',
    type: 'AI大模型',
    color: '#FF6B6B',
    bgColor: 'rgba(255, 107, 107, 0.1)'
  },
  {
    id: 'kimi',
    name: 'Kimi',
    icon: 'mdi:book-open-variant',
    description: '支持超长文本处理的AI助手，适合文档分析和理解',
    url: 'https://kimi.moonshot.cn',
    type: 'AI大模型',
    color: '#1890FF',
    bgColor: 'rgba(24, 144, 255, 0.1)'
  },
  {
    id: 'copilot',
    name: 'GitHub Copilot',
    icon: 'mdi:github',
    description: 'AI编程助手，实时提供代码建议和自动补全功能',
    url: 'https://github.com/features/copilot',
    type: 'AI编程工具',
    color: '#5E6AD2',
    bgColor: 'rgba(94, 106, 210, 0.1)'
  },
  {
    id: 'cursor',
    name: 'Trea CN',
    icon: 'mdi:cursor-default-outline',
    description: '基于AI的代码编辑器，集成deepseek，支持自然语言编程',
    url: 'https://cursor.sh',
    type: 'AI编程工具',
    color: '#000000',
    bgColor: 'rgba(0, 0, 0, 0.05)'
  },
  {
    id: 'devin',
    name: 'Devin',
    icon: 'mdi:code-braces',
    description: 'AI软件工程师，能够自主完成端到端的软件开发任务',
    url: 'https://www.cognition-labs.com/devin',
    type: 'AI编程工具',
    color: '#6366F1',
    bgColor: 'rgba(99, 102, 241, 0.1)'
  },
  {
    id: 'perplexity',
    name: 'Perplexity AI',
    icon: 'mdi:magnify',
    description: 'AI搜索引擎，提供精准的信息检索和答案生成',
    url: 'https://www.perplexity.ai',
    type: 'AI搜索工具',
    color: '#4F46E5',
    bgColor: 'rgba(79, 70, 229, 0.1)'
  },
  {
    id: 'phind',
    name: 'Phind',
    icon: 'mdi:magnify',
    description: '面向开发者的AI搜索引擎，专注于技术问题解答',
    url: 'https://www.phind.com',
    type: 'AI搜索工具',
    color: '#FF5722',
    bgColor: 'rgba(255, 87, 34, 0.1)'
  }
];

// 工具点击事件
function handleToolClick(url: string) {
  // 打开链接到新窗口
  window.open(url, '_blank', 'noopener,noreferrer');
}

// 获取URL的主机名
function getHostname(url: string) {
  try {
    return new URL(url).hostname;
  } catch {
    return url;
  }
}

// 学习技巧列表数据
interface StudyTip {
  id: string;
  title: string;
  icon: string;
  description: string;
  color: string;
}

const studyTips: StudyTip[] = [
  {
    id: 'tip1',
    title: '番茄工作法',
    icon: 'mdi:clock-outline',
    description: '25分钟工作，5分钟休息，提高专注力和工作效率',
    color: '#FF6B6B'
  },
  {
    id: 'tip2',
    title: '费曼学习法',
    icon: 'mdi:book-open-page-variant-outline',
    description: '通过讲解知识来加深理解，发现知识盲区',
    color: '#4ECDC4'
  },
  {
    id: 'tip3',
    title: '思维导图',
    icon: 'mdi:map',
    description: '用图形化方式组织知识，建立知识间的联系',
    color: '#FFD166'
  },
  {
    id: 'tip4',
    title: '间隔重复',
    icon: 'mdi:refresh',
    description: '定期复习知识，增强长期记忆效果',
    color: '#06D6A0'
  }
];
</script>

<template>
  <div class="student-tools">
    <!-- 1. 学习网页推荐区域 -->
    <NCard title="学习网页" :bordered="false">
      <div class="tools-header">
        <h2 class="tools-title">推荐学习网页</h2>
        <p class="tools-subtitle">精选优质学习网站和资源，助力高效学习</p>
      </div>

      <div class="tools-grid">
        <div
          v-for="tool in webTools"
          :key="tool.id"
          class="tool-card"
          :style="{ backgroundColor: tool.bgColor, '--card-color': tool.color }"
          @click="handleToolClick(tool.url)"
        >
          <!-- 装饰性背景元素 -->
          <div class="tool-card-bg-decoration" :style="{ backgroundColor: tool.color + '08' }"></div>

          <!-- 工具卡片头部 -->
          <div class="tool-card-header">
            <div class="tool-icon-wrapper" :style="{ backgroundColor: tool.color + '20', color: tool.color }">
              <SvgIcon :icon="tool.icon" :local-icon="tool.localIcon" class="text-40px" />
            </div>
            <NTag
              size="small"
              :style="{
                backgroundColor: tool.color + '20',
                color: tool.color,
                borderColor: tool.color + '40'
              }"
            >
              {{ tool.type }}
            </NTag>
          </div>

          <!-- 工具卡片内容 -->
          <div class="tool-card-content">
            <h3 class="tool-name">{{ tool.name }}</h3>
            <p class="tool-description">{{ tool.description }}</p>
          </div>

          <!-- 工具卡片底部 -->
          <div class="tool-card-footer">
            <div class="tool-url" :style="{ color: tool.color }">
              <SvgIcon icon="mdi:web" class="mr-1 text-sm" />
              {{ getHostname(tool.url) }}
            </div>
            <NButton
              :style="{
                backgroundColor: tool.color,
                borderColor: tool.color,
                color: '#fff',
                boxShadow: `0 2px 8px ${tool.color}40`
              }"
              size="small"
              circle
              :title="'访问' + tool.name"
              @click.stop="handleToolClick(tool.url)"
            >
              <template #icon>
                <SvgIcon icon="mdi:arrow-top-right" />
              </template>
            </NButton>
          </div>
        </div>
      </div>
    </NCard>

    <!-- 2. 学习工具列表区域 -->
    <NCard title="AI工具列表" :bordered="false" class="mt-4">
      <div class="tools-header">
        <h2 class="tools-title">AI大模型与编程工具</h2>
        <p class="tools-subtitle">精选优质AI工具，助力高效学习和编程</p>
      </div>

      <div class="tools-list">
        <div
          v-for="tool in aiTools"
          :key="tool.id"
          class="tool-list-item interactive-item"
          @click="handleToolClick(tool.url)"
        >
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <div class="tool-list-icon" :style="{ color: tool.color }">
                <SvgIcon :icon="tool.icon" :local-icon="tool.localIcon" class="text-24px" />
              </div>
              <div class="ml-4">
                <h3 class="tool-list-name font-medium">{{ tool.name }}</h3>
                <p class="tool-list-desc text-sm text-gray-500">{{ tool.description }}</p>
              </div>
            </div>
            <div class="flex items-center">
              <NTag
                size="small"
                :style="{
                  backgroundColor: tool.color + '20',
                  color: tool.color,
                  borderColor: tool.color + '40'
                }"
                class="mr-2"
              >
                {{ tool.type }}
              </NTag>
              <SvgIcon icon="mdi:arrow-right" class="text-gray-400" />
            </div>
          </div>
        </div>
      </div>
    </NCard>

    <!-- 3. 高效学习技巧区域 -->
    <NCard title="高效学习技巧" :bordered="false" class="mt-4">
      <div class="tools-header">
        <h2 class="tools-title">高效学习技巧</h2>
        <p class="tools-subtitle">掌握科学的学习方法，提升学习效率</p>
      </div>

      <div class="tips-grid">
        <div v-for="tip in studyTips" :key="tip.id" class="tip-card" :style="{ '--tip-color': tip.color }">
          <!-- 技巧卡片头部 -->
          <div class="tip-card-header">
            <div class="tip-icon-wrapper" :style="{ backgroundColor: tip.color + '20', color: tip.color }">
              <SvgIcon :icon="tip.icon" class="text-40px" />
            </div>
          </div>

          <!-- 技巧卡片内容 -->
          <div class="tip-card-content">
            <h3 class="tip-title">{{ tip.title }}</h3>
            <p class="tip-description">{{ tip.description }}</p>
          </div>

          <!-- 装饰性箭头 -->
          <div class="tip-arrow" :style="{ color: tip.color }">
            <SvgIcon icon="mdi:arrow-right" />
          </div>
        </div>
      </div>
    </NCard>
  </div>
</template>

<style scoped>
.student-tools {
  width: 100%;
  position: relative;
  overflow: visible;
  min-height: auto;
}

/* 装饰性背景元素 */
.student-tools::before {
  content: '';
  position: absolute;
  top: 0;
  right: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle at 50% 50%, rgba(102, 126, 234, 0.05) 0%, transparent 50%);
  z-index: -1;
  animation: float 20s ease-in-out infinite;
  max-height: 600px;
}

@keyframes float {
  0%,
  100% {
    transform: translate(0, 0) rotate(0deg);
  }
  33% {
    transform: translate(30px, -30px) rotate(5deg);
  }
  66% {
    transform: translate(-20px, 20px) rotate(-5deg);
  }
}

.tools-header {
  text-align: center;
  margin-bottom: 20px;
  position: relative;
}

/* 装饰性线条 */
.tools-header::before,
.tools-header::after {
  content: '';
  position: absolute;
  top: 50%;
  width: 60px;
  height: 2px;
  background: linear-gradient(90deg, transparent, rgba(102, 126, 234, 0.3));
}

.tools-header::before {
  left: 0;
  transform: translateY(-50%);
}

.tools-header::after {
  right: 0;
  transform: translateY(-50%) rotate(180deg);
}

.tools-title {
  font-size: 32px;
  font-weight: 700;
  color: #333639;
  margin-bottom: 12px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: fadeInUp 0.8s ease-out;
}

.tools-subtitle {
  font-size: 16px;
  color: #666;
  line-height: 1.5;
  animation: fadeInUp 0.8s ease-out 0.2s both;
}

/* 工具网格样式 */
.tools-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 16px;
  animation: fadeInUp 0.8s ease-out 0.4s both;
}

/* 工具卡片样式 */
.tool-card {
  display: flex;
  flex-direction: column;
  background: white;
  border-radius: 12px;
  padding: 16px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  border: 1px solid rgba(0, 0, 0, 0.05);
  position: relative;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  animation: fadeInUp 0.8s ease-out 0.6s both;
}

.tool-card:nth-child(2) {
  animation-delay: 0.7s;
}
.tool-card:nth-child(3) {
  animation-delay: 0.8s;
}
.tool-card:nth-child(4) {
  animation-delay: 0.9s;
}
.tool-card:nth-child(5) {
  animation-delay: 1s;
}
.tool-card:nth-child(6) {
  animation-delay: 1.1s;
}
.tool-card:nth-child(7) {
  animation-delay: 1.2s;
}
.tool-card:nth-child(8) {
  animation-delay: 1.3s;
}
.tool-card:nth-child(9) {
  animation-delay: 1.4s;
}

/* 卡片背景装饰 */
.tool-card-bg-decoration {
  position: absolute;
  bottom: -20px;
  right: -20px;
  width: 120px;
  height: 120px;
  border-radius: 50%;
  opacity: 0.5;
  transition: all 0.3s ease;
  z-index: 0;
}

.tool-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(90deg, var(--card-color), transparent);
  opacity: 0;
  transition: opacity 0.3s ease;
  z-index: 1;
}

.tool-card:hover {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 0 12px 32px rgba(0, 0, 0, 0.15);
}

.tool-card:hover::before {
  opacity: 1;
}

.tool-card:hover .tool-card-bg-decoration {
  transform: scale(1.2);
  opacity: 0.8;
}

/* 卡片头部样式 */
.tool-card-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 20px;
  position: relative;
  z-index: 1;
}

.tool-icon-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 80px;
  height: 80px;
  border-radius: 16px;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  z-index: 1;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

.tool-card:hover .tool-icon-wrapper {
  transform: scale(1.15) rotate(5deg);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
}

/* 卡片内容样式 */
.tool-card-content {
  flex: 1;
  margin-bottom: 20px;
  position: relative;
  z-index: 1;
}

.tool-name {
  font-size: 20px;
  font-weight: 600;
  color: #333639;
  margin-bottom: 12px;
  transition: color 0.3s ease;
  position: relative;
}

.tool-card:hover .tool-name {
  color: var(--card-color);
}

.tool-description {
  font-size: 14px;
  color: #666;
  line-height: 1.7;
  margin-bottom: 0;
  transition: color 0.3s ease;
}

.tool-card:hover .tool-description {
  color: #444;
}

/* 卡片底部样式 */
.tool-card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 16px;
  border-top: 1px solid rgba(0, 0, 0, 0.05);
  position: relative;
  z-index: 1;
}

.tool-url {
  font-size: 13px;
  display: flex;
  align-items: center;
  font-weight: 500;
  transition: all 0.3s ease;
}

/* 学习技巧区域样式 */
.tips-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
  gap: 16px;
  animation: fadeInUp 0.8s ease-out 0.6s both;
  margin-top: 24px;
}

.tip-card {
  background: white;
  border-radius: 12px;
  padding: 16px;
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(0, 0, 0, 0.05);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  flex-direction: column;
  animation: fadeInUp 0.8s ease-out 0.8s both;
}

.tip-card:nth-child(2) {
  animation-delay: 0.9s;
}
.tip-card:nth-child(3) {
  animation-delay: 1s;
}
.tip-card:nth-child(4) {
  animation-delay: 1.1s;
}

.tip-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 4px;
  height: 100%;
  background: var(--tip-color);
  transition: transform 0.3s ease;
  transform: scaleY(0);
  transform-origin: top;
}

.tip-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
}

.tip-card:hover::before {
  transform: scaleY(1);
}

.tip-card-header {
  margin-bottom: 16px;
  position: relative;
  z-index: 1;
}

.tip-icon-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 60px;
  border-radius: 12px;
  transition: all 0.3s ease;
  margin-bottom: 12px;
}

.tip-card:hover .tip-icon-wrapper {
  transform: scale(1.1);
}

.tip-card-content {
  flex: 1;
  position: relative;
  z-index: 1;
}

.tip-title {
  font-size: 18px;
  font-weight: 600;
  color: #333639;
  margin-bottom: 8px;
  transition: color 0.3s ease;
}

.tip-card:hover .tip-title {
  color: var(--tip-color);
}

.tip-description {
  font-size: 14px;
  color: #666;
  line-height: 1.6;
  margin-bottom: 0;
}

.tip-arrow {
  position: absolute;
  bottom: 16px;
  right: 16px;
  opacity: 0.3;
  transition: all 0.3s ease;
  font-size: 24px;
}

.tip-card:hover .tip-arrow {
  opacity: 1;
  transform: translateX(10px);
}

/* 动画效果 */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 学习工具列表样式 */
.tools-list {
  margin-top: 16px;
}

/* 学习工具列表项样式 */
.tool-list-item {
  padding: 16px;
  border-radius: 12px;
  background: white;
  border: 1px solid rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
  cursor: pointer;
  margin-bottom: 8px;
}

.tool-list-item:last-child {
  margin-bottom: 0;
}

.tool-list-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  border-color: rgba(100, 108, 255, 0.2);
}

/* 工具列表图标样式 */
.tool-list-icon {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(100, 108, 255, 0.05);
  border-radius: 8px;
}

/* 工具列表名称样式 */
.tool-list-name {
  font-size: 16px;
  font-weight: 500;
  color: #333639;
  margin-bottom: 4px;
}

/* 工具列表描述样式 */
.tool-list-desc {
  font-size: 14px;
  color: #666;
  line-height: 1.4;
}

/* 图标大小 */
.text-40px {
  font-size: 40px;
}

.text-24px {
  font-size: 24px;
}

.text-sm {
  font-size: 14px;
}

/* 间距工具类 */
.mt-4 {
  margin-top: 16px;
}

.mr-1 {
  margin-right: 4px;
}

.mr-2 {
  margin-right: 8px;
}

.ml-4 {
  margin-left: 16px;
}

/* 布局工具类 */
.flex {
  display: flex;
}

.items-center {
  align-items: center;
}

.justify-between {
  justify-content: space-between;
}

.font-medium {
  font-weight: 500;
}

.text-gray-500 {
  color: #666;
}

.text-gray-400 {
  color: #999;
}

/* 交互项样式 */
.interactive-item {
  transition: all 0.3s ease;
}

.interactive-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

/* 响应式设计 */
@media (max-width: 1200px) {
  .tools-header::before,
  .tools-header::after {
    display: none;
  }
}

@media (max-width: 768px) {
  .tools-grid,
  .tips-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  .tool-card,
  .tip-card {
    padding: 20px;
  }

  .tools-title {
    font-size: 28px;
  }

  .tools-subtitle {
    font-size: 16px;
  }

  .tools-header {
    margin-bottom: 32px;
  }
}
</style>
