<script setup lang="ts">
import { computed, onMounted, ref, watch } from 'vue';
import type { TimelineItemProps } from 'naive-ui';
import { useEcharts } from '@/hooks/common/echarts';
import { useRouterPush } from '@/hooks/common/router';
import SvgIcon from '@/components/custom/svg-icon.vue';

// --- 模拟数据 ---

interface TimelineItem {
  id: number;
  time: string;
  title: string;
  type: TimelineItemProps['type'];
}

const kpiData = ref([
  { label: '总学习时长 (小时)', value: 128, icon: 'ph:timer-duotone' },
  { label: '已完成课程', value: '3 / 10', icon: 'ph:check-circle-duotone' },
  { label: '平均成绩', value: 89.5, icon: 'ph:exam-duotone' },
  { label: '本周排名', value: '12', icon: 'ph:trophy-duotone' },
  { label: '获得徽章', value: '4', icon: 'ph:medal-duotone' }
]);

const timelineData = ref<TimelineItem[]>([
  { id: 1, time: '2025-10-28 10:30', title: '完成了《高等数学》的“第一章：极限与连续”', type: 'success' },
  { id: 2, time: '2025-10-27 15:00', title: '完成了“线性代数”的单元测验，得分 95', type: 'info' },
  { id: 3, time: '2025-10-26 20:00', title: '观看了课程《JavaScript 入门到精通》', type: 'default' },
  { id: 4, time: '2025-10-25 18:45', title: '向 AI 助教提问了关于“JavaScript 闭包”的问题', type: 'default' }
]);

const learningGoals = ref([
  { id: 1, title: '完成《Vue 3 深入剖析》课程', current: 75, target: 100, unit: '%' },
  { id: 2, title: '本周学习时长达到 10 小时', current: 8, target: 10, unit: '小时' },
  { id: 3, title: '完成 5 次编程练习', current: 2, target: 5, unit: '次' }
]);

const achievementBadges = ref([
  { id: 1, name: '初学者', icon: 'ph:student-duotone', earned: true, description: '完成第一个课程' },
  { id: 2, name: '持续学习者', icon: 'ph:calendar-check-duotone', earned: true, description: '连续学习7天' },
  { id: 3, name: '数学高手', icon: 'ph:function-duotone', earned: false, description: '高等数学课程获得A+' },
  { id: 4, name: '探索者', icon: 'ph:compass-duotone', earned: true, description: '学习超过3个不同领域的课程' },
  { id: 5, name: '满分学霸', icon: 'ph:seal-check-duotone', earned: false, description: '在一次重要考试中获得满分' },
  { id: 6, name: '代码新星', icon: 'ph:code-duotone', earned: true, description: '首次完成一个编程练习' },
  { id: 7, name: '周末学霸', icon: 'ph:coffee-duotone', earned: false, description: '在一个周末完成超过5小时的学习' },
  { id: 8, name: '提问达人', icon: 'ph:question-duotone', earned: true, description: '向AI助教累计提问超过20次' },
  { id: 9, name: '网络先锋', icon: 'ph:globe-duotone', earned: false, description: '完成《计算机网络》课程' },
  { id: 10, name: '全能王者', icon: 'ph:crown-duotone', earned: false, description: '解锁所有其他成就徽章' }
]);

// --- 图表配置与交互逻辑 ---

const timeDimension = ref('week');
const pieData = {
  week: [
    { value: 40, name: '高等数学' },
    { value: 25, name: '大学英语' },
    { value: 35, name: '计算机网络' }
  ],
  month: [
    { value: 120, name: '高等数学' },
    { value: 80, name: '大学英语' },
    { value: 100, name: '计算机网络' }
  ]
};
const { domRef: pieRef, setOptions: setPieOptions } = useEcharts(() => ({
  title: { text: '学科学习时长分布', left: 'center', show: false },
  tooltip: { trigger: 'item' },
  legend: { orient: 'vertical', left: 'left', top: '10%' },
  series: [
    {
      name: '学习时长',
      type: 'pie',
      radius: ['40%', '70%'],
      center: ['65%', '50%'],
      data: pieData.week,
      emphasis: { itemStyle: { shadowBlur: 10, shadowOffsetX: 0, shadowColor: 'rgba(0, 0, 0, 0.5)' } }
    }
  ]
}));

watch(timeDimension, newValue => {
  setPieOptions({
    title: { text: '学科学习时长分布', left: 'center', show: false },
    tooltip: { trigger: 'item' },
    legend: { orient: 'vertical', left: 'left', top: '10%' },
    series: [
      {
        name: '学习时长',
        type: 'pie',
        radius: ['40%', '70%'],
        center: ['65%', '50%'],
        data: pieData[newValue as keyof typeof pieData],
        emphasis: { itemStyle: { shadowBlur: 10, shadowOffsetX: 0, shadowColor: 'rgba(0, 0, 0, 0.5)' } }
      }
    ]
  });
});

const { domRef: doughnutRef } = useEcharts(() => ({
  tooltip: { trigger: 'item' },
  legend: { orient: 'vertical', left: 'left', top: '10%' },
  series: [
    {
      name: '学习方式',
      type: 'pie',
      radius: ['40%', '70%'],
      center: ['65%', '50%'],
      avoidLabelOverlap: false,
      itemStyle: {
        borderRadius: 10,
        borderColor: '#fff',
        borderWidth: 2
      },
      label: { show: false },
      emphasis: {
        label: {
          show: true,
          fontSize: 20,
          fontWeight: 'bold'
        }
      },
      labelLine: { show: false },
      data: [
        { value: 45, name: '观看视频' },
        { value: 25, name: '阅读文档' },
        { value: 20, name: '完成测验' },
        { value: 10, name: '编程练习' }
      ]
    }
  ]
}));

const showAverage = ref(false);
const myScores = [88, 92, 85, 95];
const averageScores = [85, 88, 86, 90];
const { domRef: lineRef, setOptions: setLineOptions } = useEcharts(() => ({
  title: { text: '近期成绩趋势', left: 'center' },
  tooltip: { trigger: 'axis' },
  legend: { data: ['我的成绩', '班级平均分'], top: '10%' },
  grid: { top: '25%', left: '3%', right: '4%', bottom: '3%', containLabel: true },
  xAxis: {
    type: 'category',
    data: ['第一次测验', '第二次测验', '期中考试', '第三次测验']
  },
  yAxis: { type: 'value', min: 60 },
  series: [
    { name: '我的成绩', data: myScores, type: 'line', smooth: true },
    { name: '班级平均分', data: [] as number[], type: 'line', smooth: true, lineStyle: { type: 'dashed' } }
  ]
}));

watch(showAverage, newValue => {
  setLineOptions({
    title: { text: '近期成绩趋势', left: 'center' },
    tooltip: { trigger: 'axis' },
    legend: { data: ['我的成绩', '班级平均分'], top: '10%' },
    grid: { top: '25%', left: '3%', right: '4%', bottom: '3%', containLabel: true },
    xAxis: {
      type: 'category',
      data: ['第一次测验', '第二次测验', '期中考试', '第三次测验']
    },
    yAxis: { type: 'value', min: 60 },
    series: [
      { name: '我的成绩', data: myScores, type: 'line', smooth: true },
      {
        name: '班级平均分',
        data: newValue ? averageScores : [],
        type: 'line',
        smooth: true,
        lineStyle: { type: 'dashed' }
      }
    ]
  });
});

const { domRef: radarRef } = useEcharts(() => ({
  title: {
    text: '分学科能力评估',
    left: 'center'
  },
  tooltip: { trigger: 'item' },
  legend: {
    data: ['我的成绩'],
    bottom: '2%'
  },
  radar: {
    indicator: [
      { name: '高等数学', max: 100 },
      { name: '大学英语', max: 100 },
      { name: '计算机网络', max: 100 },
      { name: '数据结构', max: 100 },
      { name: '操作系统', max: 100 }
    ],
    center: ['50%', '55%']
  },
  series: [
    {
      name: '学科成绩',
      type: 'radar',
      data: [{ value: [92, 88, 78, 85, 95], name: '我的成绩' }]
    }
  ]
}));

// 路由跳转方法
const { routerPushByKey } = useRouterPush();

// 学习提示语数组
const studyTips = [
  '今日事今日毕，别让拖延成为习惯。',
  '温故而知新，记得复习昨天学过的内容。',
  '设定一个小目标，然后去完成它！',
  '分享是最好的学习方式，试着把新知识讲给别人听。',
  '保持好奇心，它是探索知识的源动力。'
];

// 随机选择一条提示语
const randomTip = computed(() => studyTips[Math.floor(Math.random() * studyTips.length)]);

const myCoursesProgress = ref([
  { id: 'c1', name: '高等数学', progress: 75, icon: 'ph:function-duotone' },
  { id: 'c2', name: '大学英语', progress: 90, icon: 'ph:translate-duotone' },
  { id: 'c3', name: '计算机网络', progress: 50, icon: 'ph:globe-hemisphere-west-duotone' },
  { id: 'c4', name: '数据结构', progress: 82, icon: 'ph:tree-structure-duotone' }
]);

// 新增：近期测验记录数据
const recentTests = ref([
  { id: 't1', name: '高等数学 - 单元测验(一)', score: 95, date: '2025-10-27' },
  { id: 't2', name: '大学英语 - 听力测试', score: 88, date: '2025-10-25' },
  { id: 't3', name: '计算机网络 - 随堂练习', score: 78, date: '2025-10-24' },
  { id: 't4', name: 'java - 随堂练习', score: 99, date: '2025-10-24' }
]);

// onMounted 逻辑
onMounted(() => {
  const items = document.querySelectorAll<HTMLElement>('.interactive-item');

  items.forEach(item => {
    item.addEventListener('mousemove', (e: MouseEvent) => {
      const rect = (e.currentTarget as HTMLElement).getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;

      (e.currentTarget as HTMLElement).style.setProperty('--ripple-x', `${x}px`);
      (e.currentTarget as HTMLElement).style.setProperty('--ripple-y', `${y}px`);
    });
  });
});
</script>

<template>
  <div class="learning-progress-container">
    <!-- 装饰性背景元素 -->
    <div class="background-decoration"></div>

    <NSpace vertical :size="12">
      <!-- 1. 欢迎问候区 -->
      <NCard :bordered="false" class="interactive-item card-wrapper">
        <div class="card-decoration"></div>
        <NGrid :x-gap="16" :y-gap="16" cols="1 s:1 m:1 l:3" responsive="screen">
          <!-- 左侧：问候语和操作按钮 -->
          <NGi span="1 l:2">
            <div class="h-full flex flex-col justify-center">
              <h2 class="text-2xl font-bold" style="color: #64b5f6">你好, fgwh!</h2>
              <p class="mt-2 text-gray-500">今日学习小贴士：{{ randomTip }}</p>
              <NSpace class="mt-6">
                <NButton
                  type="primary"
                  size="large"
                  class="animate-pulse"
                  @click="routerPushByKey('student_dashboard')"
                >
                  开始新课程
                </NButton>
                <NButton size="large" @click="routerPushByKey('student_ai-tutor')">咨询AI助教</NButton>
              </NSpace>
            </div>
          </NGi>

          <!-- 右侧：插图 -->
          <NGi span="1 l:1" class="l:flex hidden items-center justify-center">
            <SvgIcon local-icon="banner" class="text-300px text-primary" />
          </NGi>
        </NGrid>
      </NCard>

      <!-- 2. 核心指标区 -->
      <NCard :bordered="false" class="card-wrapper">
        <div class="card-decoration"></div>
        <NGrid :x-gap="12" :y-gap="12" cols="2 s:3 m:5" responsive="screen">
          <NGi v-for="(item, index) in kpiData" :key="index">
            <div class="kpi-item interactive-item">
              <div class="kpi-icon-bg"></div>
              <div class="flex items-center text-gray-500">
                <SvgIcon :icon="item.icon" class="mr-2 text-lg" />
                <span>{{ item.label }}</span>
              </div>
              <p class="mt-1 text-2xl font-semibold">{{ item.value }}</p>
            </div>
          </NGi>
        </NGrid>
      </NCard>

      <!-- 3. 图表分析区 -->
      <div class="charts-section">
        <NGrid :x-gap="12" :y-gap="12" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
          <NGi>
            <NCard title="学习投入分析" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <NTabs type="line" animated>
                <NTabPane name="duration" tab="时长分布">
                  <template #tab>
                    <NRadioGroup v-model:value="timeDimension" name="radiogroup" size="small">
                      <NRadioButton value="week">本周</NRadioButton>
                      <NRadioButton value="month">本月</NRadioButton>
                    </NRadioGroup>
                  </template>
                  <div ref="pieRef" class="h-280px w-full"></div>
                </NTabPane>
                <NTabPane name="method" tab="方式分布">
                  <div ref="doughnutRef" class="h-280px w-full"></div>
                </NTabPane>
              </NTabs>
            </NCard>
          </NGi>
          <NGi>
            <NCard title="成绩趋势分析" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <template #header-extra>
                <NCheckbox v-model:checked="showAverage">对比班级平均分</NCheckbox>
              </template>
              <div ref="lineRef" class="h-280px w-full"></div>
            </NCard>
          </NGi>
        </NGrid>

        <NGrid :x-gap="12" :y-gap="12" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
          <NGi>
            <NCard title="分学科能力评估" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <div ref="radarRef" class="h-280px w-full"></div>
            </NCard>
          </NGi>
          <NGi>
            <NCard title="学习活动时间线" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <NScrollbar style="max-height: 280px">
                <NTimeline>
                  <NTimelineItem
                    v-for="item in timelineData"
                    :key="item.id"
                    :type="item.type"
                    :title="item.title"
                    :time="item.time"
                    class="interactive-item"
                  />
                </NTimeline>
              </NScrollbar>
            </NCard>
          </NGi>
        </NGrid>
      </div>

      <!-- 学习状态区 -->
      <div class="study-status-section">
        <NGrid :x-gap="12" :y-gap="12" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
          <!-- 左侧：我的课程进度 -->
          <NGi>
            <NCard title="我的课程进度" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <NSpace vertical :size="12">
                <div v-for="course in myCoursesProgress" :key="course.id" class="interactive-item">
                  <div class="mb-2 flex items-center">
                    <SvgIcon :icon="course.icon" class="mr-2 text-lg text-primary" />
                    <span class="font-semibold">{{ course.name }}</span>
                    <span class="ml-auto text-sm text-gray-500">{{ course.progress }}%</span>
                  </div>
                  <NProgress type="line" :percentage="course.progress" status="success" :show-indicator="false" />
                </div>
              </NSpace>
            </NCard>
          </NGi>

          <!-- 右侧：近期测验记录 -->
          <NGi>
            <NCard title="近期测验记录" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <NList hoverable clickable size="small">
                <NListItem v-for="test in recentTests" :key="test.id" class="interactive-item">
                  <NThing>
                    <template #header>
                      <div class="flex items-center justify-between">
                        <span class="font-medium">{{ test.name }}</span>
                        <NTag
                          :type="test.score >= 90 ? 'success' : test.score >= 60 ? 'info' : 'error'"
                          size="small"
                          round
                        >
                          {{ test.score }}分
                        </NTag>
                      </div>
                    </template>
                    <template #description>
                      <span class="text-xs text-gray-500">考试日期: {{ test.date }}</span>
                    </template>
                  </NThing>
                </NListItem>
              </NList>
            </NCard>
          </NGi>
        </NGrid>
      </div>

      <!-- 4. 目标与激励区 -->
      <div class="goals-section">
        <NGrid :x-gap="12" :y-gap="12" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
          <NGi>
            <NCard title="我的学习目标" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <NSpace vertical :size="12">
                <div v-for="goal in learningGoals" :key="goal.id" class="interactive-item">
                  <div class="mb-1 flex items-center justify-between">
                    <span>{{ goal.title }}</span>
                    <span class="text-sm font-medium">
                      {{ goal.current }}{{ goal.unit }} / {{ goal.target }}{{ goal.unit }}
                    </span>
                  </div>
                  <NProgress type="line" :percentage="(goal.current / goal.target) * 100" :show-indicator="false" />
                </div>
              </NSpace>
            </NCard>
          </NGi>
          <NGi>
            <NCard title="成就徽章墙" :bordered="false" class="interactive-item h-full card-wrapper">
              <div class="card-decoration"></div>
              <NGrid :x-gap="12" :y-gap="12" cols="3 s:4 m:5" responsive="screen">
                <NGi v-for="badge in achievementBadges" :key="badge.id" class="interactive-item flex-col-center">
                  <NTooltip trigger="hover">
                    <template #trigger>
                      <div class="badge-wrapper">
                        <SvgIcon
                          :icon="badge.icon"
                          class="text-4xl"
                          :class="[badge.earned ? 'text-primary' : 'text-gray-400']"
                        />
                        <div v-if="badge.earned" class="badge-glow"></div>
                      </div>
                    </template>
                    {{ badge.description }}
                  </NTooltip>
                  <p class="mt-1 text-sm" :class="[badge.earned ? 'text-primary' : 'text-gray-400']">
                    {{ badge.name }}
                  </p>
                </NGi>
              </NGrid>
            </NCard>
          </NGi>
        </NGrid>
      </div>
    </NSpace>
  </div>
</template>

<style scoped>
/* 容器样式 */
.learning-progress-container {
  position: relative;
  padding: 12px;
  background: linear-gradient(135deg, rgba(245, 247, 250, 0.5), rgba(250, 250, 252, 0.5));
  border-radius: 12px;
  overflow: visible;
  min-height: 100vh;
}

/* 背景装饰 */
.background-decoration {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background:
    radial-gradient(circle at 20% 50%, rgba(102, 126, 234, 0.05) 0%, transparent 50%),
    radial-gradient(circle at 80% 20%, rgba(100, 108, 255, 0.03) 0%, transparent 50%),
    radial-gradient(circle at 60% 80%, rgba(100, 108, 255, 0.04) 0%, transparent 50%);
  z-index: 0;
  pointer-events: none;
}

/* 卡片容器样式 */
.card-wrapper {
  min-height: 120px;
  position: relative;
  overflow: hidden;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.04);
  transition: box-shadow 0.3s ease;
}

.card-wrapper:hover {
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

/* 卡片装饰元素 */
.card-decoration {
  position: absolute;
  top: 0;
  right: 0;
  width: 120px;
  height: 120px;
  background: radial-gradient(circle, rgba(100, 108, 255, 0.1) 0%, transparent 70%);
  opacity: 0.5;
  border-radius: 50%;
  transform: translate(50%, -50%);
  z-index: 0;
  pointer-events: none;
  transition: all 0.3s ease;
}

.card-wrapper:hover .card-decoration {
  transform: translate(50%, -50%) scale(1.2);
  opacity: 0.8;
}

/* 交互元素样式 */
.interactive-item {
  position: relative;
  overflow: hidden;
  z-index: 1;
  transition: transform 0.3s ease-in-out;
  border-radius: 8px;
  padding: 12px;
  min-height: 50px;
}

.interactive-item:hover {
  transform: scale(1.02);
  z-index: 10;
}

.interactive-item::after {
  content: '';
  position: absolute;
  top: var(--ripple-y);
  left: var(--ripple-x);
  width: 0;
  height: 0;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  background: rgba(100, 108, 255, 0.15);
  opacity: 0.6;
  transition: 0s;
  pointer-events: none;
  z-index: 1;
}

.interactive-item:hover::after {
  width: 300px;
  height: 300px;
  opacity: 0;
  transition:
    width 0.6s ease-out,
    height 0.6s ease-out,
    opacity 0.6s ease-out;
}

/* 区域容器样式 */
.charts-section,
.study-status-section,
.goals-section {
  position: relative;
}

/* KPI指标项样式 */
.kpi-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: white;
  border: 1px solid rgba(100, 108, 255, 0.15);
  border-radius: 12px;
  padding: 16px 8px;
  text-align: center;
  min-height: 90px;
  justify-content: center;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  position: relative;
  overflow: hidden;
  z-index: 1;
}

/* KPI图标背景 */
.kpi-icon-bg {
  position: absolute;
  top: 8px;
  left: 50%;
  transform: translateX(-50%);
  width: 40px;
  height: 40px;
  background: radial-gradient(circle, rgba(100, 108, 255, 0.1) 0%, transparent 70%);
  border-radius: 50%;
  z-index: 0;
  opacity: 0.5;
}

/* 为每个KPI项添加不同的左侧边框和图标颜色 */
.kpi-item:nth-child(1) {
  border-left-color: #64b5f6;
}

.kpi-item:nth-child(1) .iconify {
  color: #64b5f6;
}

.kpi-item:nth-child(2) {
  border-left-color: #81c784;
}

.kpi-item:nth-child(2) .iconify {
  color: #81c784;
}

.kpi-item:nth-child(3) {
  border-left-color: #ffb74d;
}

.kpi-item:nth-child(3) .iconify {
  color: #ffb74d;
}

.kpi-item:nth-child(4) {
  border-left-color: #f06292;
}

.kpi-item:nth-child(4) .iconify {
  color: #f06292;
}

.kpi-item:nth-child(5) {
  border-left-color: #9575cd;
}

.kpi-item:nth-child(5) .iconify {
  color: #9575cd;
}

/* KPI项悬停效果 */
.kpi-item:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  transform: translateY(-2px);
  border-color: rgba(100, 108, 255, 0.3);
}

/* KPI项内部文本样式 */
.kpi-item p {
  margin-top: 8px;
  font-size: 22px;
  font-weight: 600;
  color: #333639;
  z-index: 1;
  position: relative;
}

.kpi-item span {
  font-size: 13px;
  color: #666;
  line-height: 1.4;
  z-index: 1;
  position: relative;
}

/* 徽章包装器 */
.badge-wrapper {
  position: relative;
  display: inline-block;
}

/* 徽章发光效果 */
.badge-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80px;
  height: 80px;
  background: radial-gradient(circle, rgba(100, 108, 255, 0.3) 0%, transparent 70%);
  border-radius: 50%;
  opacity: 0.6;
  animation: pulse 2s ease-in-out infinite;
  z-index: -1;
}

@keyframes pulse {
  0%,
  100% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 0.6;
  }
  50% {
    transform: translate(-50%, -50%) scale(1.1);
    opacity: 0.3;
  }
}

/* 图表容器 */
.h-280px {
  height: 280px;
}

/* 垂直居中 */
.flex-col-center {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

/* 响应式设计优化 */
@media (max-width: 768px) {
  .learning-progress-container {
    padding: 8px;
  }

  .card-wrapper {
    min-height: 100px;
  }

  .kpi-item {
    min-height: 80px;
    padding: 12px 8px;
  }

  .kpi-item p {
    font-size: 20px;
  }

  .kpi-item span {
    font-size: 12px;
  }

  .h-280px {
    height: 240px;
  }
}
</style>
