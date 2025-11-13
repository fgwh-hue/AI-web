<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import type { TimelineItemProps } from 'naive-ui';
import { useEcharts } from '@/hooks/common/echarts';
import LearningHeader from './modules/LearningHeader.vue'; // 引入新组件

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
  <NSpace vertical :size="16">
    <!-- 1. 核心指标概览 (替换为新的头部组件) -->
    <LearningHeader :kpi-data="kpiData" />

    <!-- 2. 图表分析区 -->
    <NGrid :x-gap="16" :y-gap="16" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
      <NGi>
        <NCard title="学习投入分析" :bordered="false" class="interactive-item h-full card-wrapper">
          <NTabs type="line" animated>
            <NTabPane name="duration" tab="时长分布">
              <template #tab>
                <NRadioGroup v-model:value="timeDimension" name="radiogroup" size="small">
                  <NRadioButton value="week">本周</NRadioButton>
                  <NRadioButton value="month">本月</NRadioButton>
                </NRadioGroup>
              </template>
              <div ref="pieRef" class="h-300px w-full"></div>
            </NTabPane>
            <NTabPane name="method" tab="方式分布">
              <div ref="doughnutRef" class="h-300px w-full"></div>
            </NTabPane>
          </NTabs>
        </NCard>
      </NGi>
      <NGi>
        <NCard title="成绩趋势分析" :bordered="false" class="interactive-item h-full card-wrapper">
          <template #header-extra>
            <NCheckbox v-model:checked="showAverage">对比班级平均分</NCheckbox>
          </template>
          <div ref="lineRef" class="h-300px w-full"></div>
        </NCard>
      </NGi>
    </NGrid>

    <NGrid :x-gap="16" :y-gap="16" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
      <NGi>
        <NCard title="分学科能力评估" :bordered="false" class="interactive-item h-full card-wrapper">
          <div ref="radarRef" class="h-300px w-full"></div>
        </NCard>
      </NGi>
      <NGi>
        <NCard title="学习活动时间线" :bordered="false" class="interactive-item h-full card-wrapper">
          <NScrollbar style="max-height: 300px">
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

    <NGrid :x-gap="16" :y-gap="16" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
      <!-- 左侧：我的课程进度 -->
      <NGi>
        <NCard title="我的课程进度" :bordered="false" class="interactive-item h-full card-wrapper">
          <NSpace vertical>
            <div v-for="course in myCoursesProgress" :key="course.id" class="interactive-item">
              <div class="mb-2 flex items-center">
                <SvgIcon :icon="course.icon" class="mr-2 text-lg text-primary" />
                <span class="font-semibold">{{ course.name }}</span>
              </div>
              <NProgress type="line" :percentage="course.progress" status="success" />
            </div>
          </NSpace>
        </NCard>
      </NGi>

      <!-- 右侧：近期测验记录 -->
      <NGi>
        <NCard title="近期测验记录" :bordered="false" class="interactive-item h-full card-wrapper">
          <NList hoverable clickable>
            <NListItem v-for="test in recentTests" :key="test.id" class="interactive-item">
              <NThing>
                <template #header>
                  <div class="flex items-center justify-between">
                    <span>{{ test.name }}</span>
                    <NTag :type="test.score >= 90 ? 'success' : test.score >= 60 ? 'info' : 'error'" size="small" round>
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

    <!-- 3. 目标与激励区 -->
    <NGrid :x-gap="16" :y-gap="16" cols="1 s:1 l:2" responsive="screen" item-style="display: flex;">
      <NGi>
        <NCard title="我的学习目标" :bordered="false" class="interactive-item h-full card-wrapper">
          <NSpace vertical>
            <div v-for="goal in learningGoals" :key="goal.id" class="interactive-item">
              <p class="mb-1">
                {{ goal.title }} ({{ goal.current }}{{ goal.unit }} / {{ goal.target }}{{ goal.unit }})
              </p>
              <NProgress type="line" :percentage="(goal.current / goal.target) * 100" />
            </div>
          </NSpace>
        </NCard>
      </NGi>
      <NGi>
        <NCard title="成就徽章墙" :bordered="false" class="interactive-item h-full card-wrapper">
          <NGrid :x-gap="16" :y-gap="16" cols="3 s:4 m:5" responsive="screen">
            <NGi v-for="badge in achievementBadges" :key="badge.id" class="interactive-item flex-col-center">
              <NTooltip trigger="hover">
                <template #trigger>
                  <SvgIcon
                    :icon="badge.icon"
                    class="text-4xl"
                    :class="[badge.earned ? 'text-primary' : 'text-gray-400']"
                  />
                </template>
                {{ badge.description }}
              </NTooltip>
              <p class="mt-1 text-sm" :class="[badge.earned ? 'text-primary' : 'text-gray-400']">{{ badge.name }}</p>
            </NGi>
          </NGrid>
        </NCard>
      </NGi>
    </NGrid>
  </NSpace>
</template>

<style scoped>
.card-wrapper {
  min-height: 120px;
}

.interactive-item {
  position: relative;
  overflow: hidden;
  z-index: 0;
  transition: transform 0.3s ease-in-out;
  border-radius: 8px;
  padding: 12px;
  min-height: 50px;
}

.interactive-item:hover {
  transform: scale(1.05);
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
  background: rgba(0, 0, 0, 0.1);
  opacity: 0.5;
  transition: 0s;
  pointer-events: none;
  z-index: 1;
}

.interactive-item:hover::after {
  width: 400px;
  height: 400px;
  opacity: 0;
  transition:
    width 0.6s ease-out,
    height 0.6s ease-out,
    opacity 0.6s ease-out;
}
</style>
