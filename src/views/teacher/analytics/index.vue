<script setup lang="ts">
import { computed, h, ref } from 'vue';
import type { DataTableColumns } from 'naive-ui';
import { NButton, NDescriptions, NDescriptionsItem, NProgress, NTag } from 'naive-ui';
import { useAppStore } from '@/store/modules/app';
import type { ECOption } from '@/hooks/common/echarts';
import { useEcharts } from '@/hooks/common/echarts';

defineOptions({
  name: 'TeacherAnalytics'
});

const appStore = useAppStore();

const gap = computed(() => (appStore.isMobile ? 0 : 16));

// 弹窗状态
const showAllAlertsModal = ref(false);
const showStudentDetailModal = ref(false);
const showExportModal = ref(false);
const selectedStudent = ref<StudentAlert | null>(null);

// 时间范围选择
const timeRange = ref<'week' | 'month' | 'semester'>('week');
const timeRangeOptions = [
  { label: '本周', value: 'week' },
  { label: '本月', value: 'month' },
  { label: '本学期', value: 'semester' }
];

// 趋势分析时间范围
const trendTimeRange = ref<'week' | 'month' | 'semester'>('month');

// 切换趋势时间范围
function handleTrendTimeChange(range: 'week' | 'month' | 'semester') {
  trendTimeRange.value = range;
  let rangeText = '本学期';
  if (range === 'week') {
    rangeText = '本周';
  } else if (range === 'month') {
    rangeText = '本月';
  }
  window.$message?.success(`已切换到${rangeText}数据`);
}

// 加载状态
const loading = ref(false);

// 刷新数据
function handleRefresh() {
  loading.value = true;
  window.$message?.success('数据刷新中...');
  setTimeout(() => {
    loading.value = false;
    window.$message?.success('数据刷新成功！');
  }, 1000);
}

// 导出报告
function handleExport() {
  showExportModal.value = true;
}

// 显示消息提示
function showMessage(type: 'info' | 'success' | 'warning' | 'error', content: string) {
  window.$message?.[type](content);
}

// 确认导出
function confirmExport(format: string) {
  showMessage('success', `正在导出${format}格式报告...`);
  showExportModal.value = false;
  setTimeout(() => {
    showMessage('success', '报告导出成功！');
  }, 1500);
}

// 联系学生
function handleContactStudent() {
  showMessage('info', '正在打开联系方式...');
  showStudentDetailModal.value = false;
}

// 数据工具点击处理
function handleToolClick(message: string) {
  showMessage('info', message);
}

interface CourseAnalytics {
  name: string;
  avgScore: number;
  passRate: number;
  students: number;
  trend: string;
}

interface StudentAlert {
  id: number;
  name: string;
  avatar: string;
  failCount: number;
  lastSubmit: string;
  score: number;
  riskLevel: 'high' | 'medium' | 'low';
}

const courseAnalytics: CourseAnalytics[] = [
  { name: '高等数学', avgScore: 82, passRate: 88, students: 45, trend: 'up' },
  { name: '英语写作', avgScore: 85, passRate: 92, students: 38, trend: 'up' },
  { name: '物理实验', avgScore: 79, passRate: 84, students: 42, trend: 'down' },
  { name: '化学基础', avgScore: 81, passRate: 86, students: 31, trend: 'up' },
  { name: '计算机编程', avgScore: 88, passRate: 95, students: 52, trend: 'up' },
  { name: '数据结构', avgScore: 76, passRate: 82, students: 48, trend: 'down' }
];

// 学生学习行为分析数据 - 直接在模板中使用的模拟数据

const studentAlerts: StudentAlert[] = [
  { id: 1, name: '张三', avatar: '', failCount: 5, lastSubmit: '3天前', score: 45, riskLevel: 'high' },
  { id: 2, name: '李四', avatar: '', failCount: 3, lastSubmit: '2天前', score: 58, riskLevel: 'medium' },
  { id: 3, name: '王五', avatar: '', failCount: 2, lastSubmit: '1天前', score: 62, riskLevel: 'low' },
  { id: 4, name: '赵六', avatar: '', failCount: 4, lastSubmit: '4天前', score: 52, riskLevel: 'high' },
  { id: 5, name: '孙七', avatar: '', failCount: 2, lastSubmit: '1天前', score: 65, riskLevel: 'low' }
];

// 所有预警学生（用于"查看全部"）
const allStudentAlerts: StudentAlert[] = [
  ...studentAlerts,
  { id: 6, name: '周八', avatar: '', failCount: 3, lastSubmit: '2天前', score: 55, riskLevel: 'medium' },
  { id: 7, name: '吴九', avatar: '', failCount: 6, lastSubmit: '5天前', score: 42, riskLevel: 'high' },
  { id: 8, name: '郑十', avatar: '', failCount: 1, lastSubmit: '1天前', score: 68, riskLevel: 'low' },
  { id: 9, name: '钱十一', avatar: '', failCount: 4, lastSubmit: '3天前', score: 48, riskLevel: 'high' },
  { id: 10, name: '孙十二', avatar: '', failCount: 2, lastSubmit: '2天前', score: 63, riskLevel: 'low' }
];

// 查看全部预警学生
function handleViewAllAlerts() {
  showAllAlertsModal.value = true;
}

// 查看学生详情
function handleViewStudentDetail(student: StudentAlert) {
  selectedStudent.value = student;
  showStudentDetailModal.value = true;
}

const scoreDistribution = [
  { range: '90-100', count: 45 },
  { range: '80-89', count: 78 },
  { range: '70-79', count: 52 },
  { range: '60-69', count: 28 },
  { range: '0-59', count: 12 }
];

const dailyActivity = [
  { day: '周一', views: 320, submissions: 45, logins: 156 },
  { day: '周二', views: 380, submissions: 52, logins: 142 },
  { day: '周三', views: 420, submissions: 48, logins: 138 },
  { day: '周四', views: 350, submissions: 38, logins: 145 },
  { day: '周五', views: 280, submissions: 42, logins: 132 },
  { day: '周六', views: 180, submissions: 15, logins: 85 },
  { day: '周日', views: 150, submissions: 12, logins: 68 }
];

const weeklyTrend = [
  { week: '第1周', avgScore: 75, passRate: 82 },
  { week: '第2周', avgScore: 78, passRate: 85 },
  { week: '第3周', avgScore: 80, passRate: 87 },
  { week: '第4周', avgScore: 82, passRate: 88 }
];

const getAvatarGradient = (id: number) => {
  const gradients = [
    'linear-gradient(135deg, #667eea 0%, #764ba2 100%)',
    'linear-gradient(135deg, #f093fb 0%, #f5576c 100%)',
    'linear-gradient(135deg, #4facfe 0%, #00f2fe 100%)',
    'linear-gradient(135deg, #43e97b 0%, #38f9d7 100%)',
    'linear-gradient(135deg, #fa709a 0%, #fee140 100%)'
  ];
  return gradients[id % gradients.length];
};

const { domRef: activityDomRef } = useEcharts(() => {
  const option: ECOption = {
    tooltip: { trigger: 'axis', axisPointer: { type: 'shadow' } },
    legend: { data: ['浏览量', '提交数', '登录数'] },
    grid: { left: '3%', right: '4%', bottom: '3%', containLabel: true },
    xAxis: { type: 'category', data: dailyActivity.map(item => item.day) },
    yAxis: { type: 'value' },
    series: [
      { name: '浏览量', type: 'bar', data: dailyActivity.map(item => item.views), itemStyle: { color: '#2080f0' } },
      {
        name: '提交数',
        type: 'bar',
        data: dailyActivity.map(item => item.submissions),
        itemStyle: { color: '#18a058' }
      },
      { name: '登录数', type: 'bar', data: dailyActivity.map(item => item.logins), itemStyle: { color: '#f0a020' } }
    ]
  };
  return option;
});

const { domRef: scoreDomRef } = useEcharts(() => {
  const option: ECOption = {
    tooltip: { trigger: 'item', formatter: '{b}: {c} ({d}%)' },
    legend: { orient: 'vertical', left: 'left' },
    series: [
      {
        name: '成绩分布',
        type: 'pie',
        radius: '50%',
        data: scoreDistribution.map(item => ({ value: item.count, name: item.range })),
        emphasis: { itemStyle: { shadowBlur: 10, shadowOffsetX: 0, shadowColor: 'rgba(0, 0, 0, 0.5)' } }
      }
    ]
  };
  return option;
});

const { domRef: trendDomRef } = useEcharts(() => {
  const option: ECOption = {
    tooltip: { trigger: 'axis' },
    legend: { data: ['平均分', '及格率'] },
    grid: { left: '3%', right: '4%', bottom: '3%', containLabel: true },
    xAxis: { type: 'category', data: weeklyTrend.map(item => item.week) },
    yAxis: { type: 'value' },
    series: [
      {
        name: '平均分',
        type: 'line',
        data: weeklyTrend.map(item => item.avgScore),
        smooth: true,
        itemStyle: { color: '#2080f0' }
      },
      {
        name: '及格率',
        type: 'line',
        data: weeklyTrend.map(item => item.passRate),
        smooth: true,
        itemStyle: { color: '#18a058' }
      }
    ]
  };
  return option;
});

const alertColumns: DataTableColumns<StudentAlert> = [
  {
    title: '学生',
    key: 'name',
    width: 120,
    render: row => {
      return h('div', { class: 'flex-y-center gap-8px' }, [
        h(
          'div',
          {
            class: 'size-32px rd-1/2 flex-center text-white font-medium',
            style: { background: getAvatarGradient(row.id) }
          },
          [h('span', { class: 'text-14px' }, row.name.charAt(0))]
        ),
        h('span', row.name)
      ]);
    }
  },
  { title: '缺交次数', key: 'failCount', width: 100, render: row => h('span', `${row.failCount} 次`) },
  { title: '最后提交', key: 'lastSubmit', width: 100 },
  { title: '分数', key: 'score', width: 80, render: row => h('span', { class: 'font-bold' }, `${row.score}分`) },
  {
    title: '风险等级',
    key: 'riskLevel',
    width: 100,
    render: row => {
      const typeMap = { high: 'error', medium: 'warning', low: 'info' };
      const textMap = { high: '高风险', medium: '中风险', low: '低风险' };
      return h(NTag, { type: typeMap[row.riskLevel] as any }, { default: () => textMap[row.riskLevel] });
    }
  },
  {
    title: '操作',
    key: 'actions',
    width: 100,
    render: row =>
      h(
        NButton,
        { size: 'small', type: 'primary', text: true, onClick: () => handleViewStudentDetail(row) },
        { default: () => '查看详情' }
      )
  }
];

const courseColumns: DataTableColumns<CourseAnalytics> = [
  { title: '课程名称', key: 'name', width: 150 },
  { title: '平均分', key: 'avgScore', width: 100, render: row => h('span', { class: 'font-bold' }, row.avgScore) },
  {
    title: '及格率',
    key: 'passRate',
    width: 200,
    render: row => {
      return h('div', { class: 'flex-y-center gap-8px' }, [
        h(NProgress, { type: 'line', percentage: row.passRate, showIndicator: false }),
        h('span', { class: 'text-12px' }, `${row.passRate}%`)
      ]);
    }
  },
  { title: '学生数', key: 'students', width: 100 },
  {
    title: '趋势',
    key: 'trend',
    width: 80,
    render: row => {
      const icon = row.trend === 'up' ? 'i-mdi-trending-up' : 'i-mdi-trending-down';
      const color = row.trend === 'up' ? 'text-success' : 'text-error';
      return h('div', { class: `${icon} text-20px ${color}` });
    }
  }
];
</script>

<template>
  <NSpace vertical :size="16">
    <div class="flex-y-center justify-between">
      <div>
        <h2 class="text-20px font-semibold">数据分析</h2>
        <p class="mt-4px text-13px text-#999">实时监控教学数据，优化教学效果</p>
      </div>
      <NSpace>
        <NSelect v-model:value="timeRange" :options="timeRangeOptions" class="w-120px" />
        <NButton @click="handleExport">
          <template #icon>
            <div class="i-mdi-download" />
          </template>
          导出报告
        </NButton>
        <NButton type="primary" :loading="loading" @click="handleRefresh">
          <template #icon>
            <div class="i-mdi-refresh" />
          </template>
          刷新数据
        </NButton>
      </NSpace>
    </div>

    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <NGi span="24 s:12 m:6">
        <NCard :bordered="false" class="stat-card stat-card-primary card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">156</div>
              <div class="mt-8px text-14px opacity-80">总学生数</div>
              <div class="mt-4px text-12px opacity-60">↑ 12% 较上周</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-account-group text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:6">
        <NCard :bordered="false" class="stat-card stat-card-success card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">82.5</div>
              <div class="mt-8px text-14px opacity-80">平均分</div>
              <div class="mt-4px text-12px opacity-60">↑ 3.5 较上周</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-chart-line text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:6">
        <NCard :bordered="false" class="stat-card stat-card-warning card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">87.5%</div>
              <div class="mt-8px text-14px opacity-80">及格率</div>
              <div class="mt-4px text-12px opacity-60">↑ 2.5% 较上周</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-trophy text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:6">
        <NCard :bordered="false" class="stat-card stat-card-info card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">92%</div>
              <div class="mt-8px text-14px opacity-80">作业完成率</div>
              <div class="mt-4px text-12px opacity-60">↑ 5% 较上周</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-clipboard-check text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
    </NGrid>

    <!-- 周趋势分析 - 移到最上面 -->
    <NCard :bordered="false" class="card-wrapper">
      <template #header>
        <div class="flex-y-center justify-between">
          <span class="font-semibold">周趋势分析</span>
          <NSpace>
            <NButton
              text
              size="small"
              :type="trendTimeRange === 'week' ? 'primary' : 'default'"
              @click="handleTrendTimeChange('week')"
            >
              本周
            </NButton>
            <NButton
              text
              size="small"
              :type="trendTimeRange === 'month' ? 'primary' : 'default'"
              @click="handleTrendTimeChange('month')"
            >
              本月
            </NButton>
            <NButton
              text
              size="small"
              :type="trendTimeRange === 'semester' ? 'primary' : 'default'"
              @click="handleTrendTimeChange('semester')"
            >
              本学期
            </NButton>
          </NSpace>
        </div>
      </template>
      <div class="flex-col">
        <div ref="trendDomRef" class="h-280px" />
        <div class="mt-16px border-t pt-16px">
          <NGrid :x-gap="16" :y-gap="12" :cols="4">
            <NGi>
              <div class="text-center">
                <div class="text-24px text-primary font-bold">82.5</div>
                <div class="mt-4px text-12px text-#999">当前平均分</div>
                <div class="mt-2px text-11px text-success">↑ 3.5</div>
              </div>
            </NGi>
            <NGi>
              <div class="text-center">
                <div class="text-24px text-success font-bold">87.5%</div>
                <div class="mt-4px text-12px text-#999">当前及格率</div>
                <div class="mt-2px text-11px text-success">↑ 2.5%</div>
              </div>
            </NGi>
            <NGi>
              <div class="text-center">
                <div class="text-24px text-warning font-bold">+7.5</div>
                <div class="mt-4px text-12px text-#999">平均分增长</div>
                <div class="mt-2px text-11px text-#999">较第1周</div>
              </div>
            </NGi>
            <NGi>
              <div class="text-center">
                <div class="text-24px text-info font-bold">+5.5%</div>
                <div class="mt-4px text-12px text-#999">及格率增长</div>
                <div class="mt-2px text-11px text-#999">较第1周</div>
              </div>
            </NGi>
          </NGrid>
        </div>
      </div>
    </NCard>

    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <template #header>
            <span class="font-semibold">每日活跃度趋势</span>
          </template>
          <div ref="activityDomRef" class="h-320px" />
        </NCard>
      </NGi>

      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="card-wrapper">
          <template #header>
            <span class="font-semibold">成绩分布</span>
          </template>
          <div class="h-320px flex-col">
            <div ref="scoreDomRef" class="flex-1" />
            <div class="mt-16px border-t pt-16px">
              <NSpace vertical :size="8">
                <div class="flex-y-center justify-between">
                  <span class="text-12px text-#999">总人数</span>
                  <span class="text-14px font-bold">215</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-12px text-#999">优秀率 (≥90)</span>
                  <span class="text-14px text-success font-bold">20.9%</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-12px text-#999">不及格率 (&lt;60)</span>
                  <span class="text-14px text-error font-bold">5.6%</span>
                </div>
              </NSpace>
            </div>
          </div>
        </NCard>
      </NGi>
    </NGrid>

    <!-- 数据洞察卡片 -->
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <!-- 学生学习行为分析 -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="h-full card-wrapper">
          <template #header>
            <div class="flex-y-center justify-between">
              <div class="flex-y-center gap-8px">
                <div class="i-mdi-chart-bell-curve text-20px text-primary" />
                <span class="font-semibold">学生学习行为分析</span>
              </div>
            </div>
          </template>
          <div class="h-full space-y-16px">
            <!-- 关键指标卡片 -->
            <div class="grid grid-cols-2 gap-8px">
              <div
                class="border border-blue-100 rounded-lg from-blue-50 to-white bg-gradient-to-br p-6px text-center transition-all hover:shadow-sm"
              >
                <div class="i-mdi-clock-outline mb-2px text-20px text-blue-500" />
                <div class="text-11px text-blue-600">平均学习时长</div>
                <div class="text-18px text-blue-800 font-bold">2.5小时/天</div>
                <div class="mt-1px text-10px text-blue-500">↑ 15% 较上周</div>
              </div>
              <div
                class="border border-green-100 rounded-lg from-green-50 to-white bg-gradient-to-br p-6px text-center transition-all hover:shadow-sm"
              >
                <div class="i-mdi-login mb-2px text-20px text-green-500" />
                <div class="text-11px text-green-600">平均登录次数</div>
                <div class="text-18px text-green-800 font-bold">3.2次/天</div>
                <div class="mt-1px text-10px text-green-500">↑ 8% 较上周</div>
              </div>
              <div
                class="border border-purple-100 rounded-lg from-purple-50 to-white bg-gradient-to-br p-6px text-center transition-all hover:shadow-sm"
              >
                <div class="i-mdi-file-document-check mb-2px text-20px text-purple-500" />
                <div class="text-11px text-purple-600">作业提交率</div>
                <div class="text-18px text-purple-800 font-bold">92%</div>
                <div class="mt-1px text-10px text-purple-500">↑ 5% 较上周</div>
              </div>
              <div
                class="border border-orange-100 rounded-lg from-orange-50 to-white bg-gradient-to-br p-6px text-center transition-all hover:shadow-sm"
              >
                <div class="i-mdi-comment-processing mb-2px text-20px text-orange-500" />
                <div class="text-11px text-orange-600">互动参与度</div>
                <div class="text-18px text-orange-800 font-bold">78%</div>
                <div class="mt-1px text-10px text-orange-500">↑ 12% 较上周</div>
              </div>
            </div>

            <!-- 学习高峰时段 -->
            <div class="border border-gray-100 rounded-lg bg-white p-6px">
              <h3 class="mb-6px flex-y-center text-12px font-semibold">
                <div class="i-mdi-clock-time-five mr-3px text-14px text-primary" />
                学习高峰时段
              </h3>
              <div class="space-y-4px">
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">08:00-10:00</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="65" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">65%</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">14:00-16:00</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="85" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">85%</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">19:00-21:00</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="95" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">95%</span>
                </div>
              </div>
            </div>

            <!-- 学习设备分布 -->
            <div class="border border-gray-100 rounded-lg bg-white p-6px">
              <h3 class="mb-6px flex-y-center text-12px font-semibold">
                <div class="i-mdi-devices mr-3px text-14px text-primary" />
                学习设备分布
              </h3>
              <div class="space-y-4px">
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">桌面端</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="68" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">68%</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">移动端</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="32" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">32%</span>
                </div>
              </div>
            </div>

            <!-- 作业完成时间分布 -->
            <div class="border border-gray-100 rounded-lg bg-white p-6px">
              <h3 class="mb-6px flex-y-center text-12px font-semibold">
                <div class="i-mdi-calendar-check mr-3px text-14px text-primary" />
                作业完成时间分布
              </h3>
              <div class="grid grid-cols-2 gap-8px">
                <div
                  class="border border-green-100 rounded-lg from-green-50 to-white bg-gradient-to-br p-5px text-center transition-all hover:shadow-sm"
                >
                  <div class="text-10px text-gray-600">提前完成</div>
                  <div class="text-14px text-green-800 font-bold">45%</div>
                </div>
                <div
                  class="border border-blue-100 rounded-lg from-blue-50 to-white bg-gradient-to-br p-5px text-center transition-all hover:shadow-sm"
                >
                  <div class="text-10px text-gray-600">按时完成</div>
                  <div class="text-14px text-blue-800 font-bold">38%</div>
                </div>
                <div
                  class="border border-orange-100 rounded-lg from-orange-50 to-white bg-gradient-to-br p-5px text-center transition-all hover:shadow-sm"
                >
                  <div class="text-10px text-gray-600">逾期1天内</div>
                  <div class="text-14px text-orange-800 font-bold">12%</div>
                </div>
                <div
                  class="border border-red-100 rounded-lg from-red-50 to-white bg-gradient-to-br p-5px text-center transition-all hover:shadow-sm"
                >
                  <div class="text-10px text-gray-600">逾期1天以上</div>
                  <div class="text-14px text-red-800 font-bold">5%</div>
                </div>
              </div>
            </div>

            <!-- 学习进度分布 -->
            <div class="border border-gray-100 rounded-lg bg-white p-6px">
              <h3 class="mb-6px flex-y-center text-12px font-semibold">
                <div class="i-mdi-chart-bar mr-3px text-14px text-primary" />
                学习进度分布
              </h3>
              <div class="space-y-4px">
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">进度领先</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="28" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">28%</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">进度正常</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="52" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">52%</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">进度落后</span>
                  <div class="mx-8px flex-1">
                    <NProgress type="line" :percentage="20" :show-indicator="false" :height="5" />
                  </div>
                  <span class="text-11px font-bold">20%</span>
                </div>
              </div>
            </div>
          </div>
        </NCard>
      </NGi>

      <!-- 课程表现深度分析 -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="h-full card-wrapper">
          <template #header>
            <div class="flex-y-center justify-between">
              <div class="flex-y-center gap-8px">
                <div class="i-mdi-chart-areaspline text-20px text-primary" />
                <span class="font-semibold">课程表现深度分析</span>
              </div>
              <NButton text type="primary" size="small">查看全部</NButton>
            </div>
          </template>
          <div class="h-full space-y-8px">
            <!-- 课程表现列表 -->
            <NSpace vertical :size="8">
              <div
                v-for="(course, index) in courseAnalytics.slice(0, 4)"
                :key="index"
                class="border border-gray-100 rounded-lg bg-white p-6px transition-all hover:shadow-sm"
              >
                <div class="mb-4px flex-y-center justify-between">
                  <span class="text-12px font-semibold">{{ course.name }}</span>
                  <NTag :type="course.trend === 'up' ? 'success' : 'error'" size="small">
                    <template #icon>
                      <div :class="course.trend === 'up' ? 'i-mdi-trending-up' : 'i-mdi-trending-down'" />
                    </template>
                    {{ course.trend === 'up' ? '上升' : '下降' }}
                  </NTag>
                </div>
                <div class="space-y-3px">
                  <div class="flex-y-center justify-between">
                    <span class="text-10px text-#666">平均分</span>
                    <span class="text-11px font-bold">{{ course.avgScore }}</span>
                  </div>
                  <div class="flex-y-center justify-between">
                    <span class="text-10px text-#666">及格率</span>
                    <span class="text-11px font-bold">{{ course.passRate }}%</span>
                  </div>
                  <div class="flex-y-center justify-between">
                    <span class="text-10px text-#666">学生数</span>
                    <span class="text-11px font-bold">{{ course.students }}</span>
                  </div>
                </div>
                <div class="mt-4px">
                  <NProgress type="line" :percentage="course.passRate" :show-indicator="false" :height="5" />
                </div>
              </div>
            </NSpace>

            <!-- 课程表现对比 -->
            <div class="border border-gray-100 rounded-lg bg-white p-6px">
              <h3 class="mb-6px flex-y-center text-12px font-semibold">
                <div class="i-mdi-chart-combo mr-3px text-14px text-primary" />
                课程表现对比
              </h3>
              <div class="space-y-4px">
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">平均分最高</span>
                  <span class="text-11px text-green-800 font-bold">计算机编程 (88)</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">及格率最高</span>
                  <span class="text-11px text-green-800 font-bold">英语写作 (92%)</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">学生数最多</span>
                  <span class="text-11px text-blue-800 font-bold">计算机编程 (52人)</span>
                </div>
                <div class="flex-y-center justify-between">
                  <span class="text-11px text-#666">进步最快</span>
                  <span class="text-11px text-orange-800 font-bold">高等数学 (↑5分)</span>
                </div>
              </div>
            </div>

            <!-- 教学建议 -->
            <div class="border border-primary/10 rounded-lg from-primary/5 to-info/5 bg-gradient-to-r p-6px">
              <div class="flex-y-center gap-6px">
                <div class="i-mdi-lightbulb-on text-16px text-primary" />
                <div class="flex-1">
                  <div class="text-11px font-semibold">教学建议</div>
                  <div class="mt-2px text-10px text-#666">
                    物理实验课程及格率呈下降趋势，建议增加实验指导时间，重点关注基础薄弱学生。
                    数据结构课程平均分较低，建议增加辅导课程或调整教学方法。
                  </div>
                </div>
              </div>
            </div>
          </div>
        </NCard>
      </NGi>
    </NGrid>

    <NCard :bordered="false" class="card-wrapper">
      <template #header>
        <div class="flex-y-center justify-between">
          <span class="font-semibold">学生预警</span>
          <NButton text type="primary" @click="handleViewAllAlerts">查看全部</NButton>
        </div>
      </template>
      <NDataTable :columns="alertColumns" :data="studentAlerts" :pagination="false" />
    </NCard>

    <!-- 查看全部预警学生弹窗 -->
    <NModal v-model:show="showAllAlertsModal" preset="card" title="全部预警学生" class="w-800px">
      <NDataTable :columns="alertColumns" :data="allStudentAlerts" :pagination="{ pageSize: 10 }" />
    </NModal>

    <!-- 学生详情弹窗 -->
    <NModal v-model:show="showStudentDetailModal" preset="card" title="学生详情" class="w-600px">
      <div v-if="selectedStudent">
        <div class="mb-24px flex-center">
          <div
            class="size-80px flex-center rd-1/2 text-32px text-white font-bold"
            :style="{ background: getAvatarGradient(selectedStudent.id) }"
          >
            {{ selectedStudent.name.charAt(0) }}
          </div>
        </div>

        <NDescriptions bordered :column="2">
          <NDescriptionsItem label="姓名">{{ selectedStudent.name }}</NDescriptionsItem>
          <NDescriptionsItem label="学号">{{ `2024${String(selectedStudent.id).padStart(4, '0')}` }}</NDescriptionsItem>
          <NDescriptionsItem label="当前分数">
            <span class="text-18px font-bold">{{ selectedStudent.score }}分</span>
          </NDescriptionsItem>
          <NDescriptionsItem label="风险等级">
            <NTag
              :type="
                selectedStudent.riskLevel === 'high'
                  ? 'error'
                  : selectedStudent.riskLevel === 'medium'
                    ? 'warning'
                    : 'info'
              "
            >
              {{
                selectedStudent.riskLevel === 'high'
                  ? '高风险'
                  : selectedStudent.riskLevel === 'medium'
                    ? '中风险'
                    : '低风险'
              }}
            </NTag>
          </NDescriptionsItem>
          <NDescriptionsItem label="缺交次数">{{ selectedStudent.failCount }} 次</NDescriptionsItem>
          <NDescriptionsItem label="最后提交">{{ selectedStudent.lastSubmit }}</NDescriptionsItem>
          <NDescriptionsItem label="班级" :span="2">计算机科学与技术 2024-1班</NDescriptionsItem>
          <NDescriptionsItem label="联系方式" :span="2">
            {{ `138${String(selectedStudent.id).padStart(8, '0')}` }}
          </NDescriptionsItem>
        </NDescriptions>

        <div class="mt-24px">
          <h3 class="mb-12px text-16px font-semibold">近期作业情况</h3>
          <NSpace vertical :size="12">
            <div class="flex-y-center justify-between border-b pb-8px">
              <span>第5章练习</span>
              <NTag type="error">未提交</NTag>
            </div>
            <div class="flex-y-center justify-between border-b pb-8px">
              <span>英文作文</span>
              <NTag type="success">已提交 (85分)</NTag>
            </div>
            <div class="flex-y-center justify-between border-b pb-8px">
              <span>实验报告</span>
              <NTag type="error">未提交</NTag>
            </div>
          </NSpace>
        </div>

        <div class="mt-24px flex justify-end gap-12px">
          <NButton @click="showStudentDetailModal = false">关闭</NButton>
          <NButton type="primary" @click="handleContactStudent">
            <template #icon>
              <div class="i-mdi-phone" />
            </template>
            联系学生
          </NButton>
        </div>
      </div>
    </NModal>

    <NCard :bordered="false" class="card-wrapper">
      <template #header>
        <span class="font-semibold">课程分析</span>
      </template>
      <NDataTable :columns="courseColumns" :data="courseAnalytics" :pagination="{ pageSize: 5 }" />
    </NCard>

    <!-- 数据分析工具 -->
    <NCard :bordered="false" class="card-wrapper">
      <template #header>
        <span class="font-semibold">数据分析工具</span>
      </template>
      <NGrid :x-gap="16" :y-gap="16" responsive="screen" item-responsive>
        <NGi span="12 s:8 m:4">
          <div class="quick-action-card" @click="handleToolClick('正在打开数据对比功能...')">
            <div class="i-mdi-chart-combo text-32px text-primary" />
            <span class="mt-8px text-13px">数据对比</span>
          </div>
        </NGi>
        <NGi span="12 s:8 m:4">
          <div class="quick-action-card" @click="handleToolClick('正在打开高级筛选功能...')">
            <div class="i-mdi-filter-outline text-32px text-success" />
            <span class="mt-8px text-13px">高级筛选</span>
          </div>
        </NGi>
        <NGi span="12 s:8 m:4">
          <div class="quick-action-card" @click="handleExport">
            <div class="i-mdi-file-chart text-32px text-warning" />
            <span class="mt-8px text-13px">报告生成</span>
          </div>
        </NGi>
        <NGi span="12 s:8 m:4">
          <div class="quick-action-card" @click="handleExport">
            <div class="i-mdi-download text-32px text-info" />
            <span class="mt-8px text-13px">数据导出</span>
          </div>
        </NGi>
        <NGi span="12 s:8 m:4">
          <div class="quick-action-card" @click="handleToolClick('正在打开趋势预测功能...')">
            <div class="i-mdi-trending-up text-32px text-error" />
            <span class="mt-8px text-13px">趋势预测</span>
          </div>
        </NGi>
        <NGi span="12 s:8 m:4">
          <div class="quick-action-card" @click="handleToolClick('正在打开数据分享功能...')">
            <div class="i-mdi-share-variant text-32px text-#666" />
            <span class="mt-8px text-13px">数据分享</span>
          </div>
        </NGi>
      </NGrid>
    </NCard>

    <!-- 导出报告弹窗 -->
    <NModal v-model:show="showExportModal" preset="dialog" title="导出报告">
      <div class="py-20px">
        <p class="mb-16px text-14px text-#666">请选择导出格式：</p>
        <NSpace vertical :size="12">
          <NButton block @click="confirmExport('PDF')">
            <template #icon>
              <div class="i-mdi-file-pdf-box" />
            </template>
            导出为 PDF
          </NButton>
          <NButton block @click="confirmExport('Excel')">
            <template #icon>
              <div class="i-mdi-file-excel" />
            </template>
            导出为 Excel
          </NButton>
          <NButton block @click="confirmExport('Word')">
            <template #icon>
              <div class="i-mdi-file-word" />
            </template>
            导出为 Word
          </NButton>
        </NSpace>
      </div>
    </NModal>
  </NSpace>
</template>

<style scoped>
.stat-card {
  color: white;
  transition: transform 0.3s;
}

.stat-card:hover {
  transform: translateY(-4px);
}

.stat-card-primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.stat-card-success {
  background: linear-gradient(135deg, #18a058 0%, #0e7e3e 100%);
}

.stat-card-warning {
  background: linear-gradient(135deg, #f0a020 0%, #d48806 100%);
}

.stat-card-info {
  background: linear-gradient(135deg, #2080f0 0%, #1060c9 100%);
}

.quick-action-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px 12px;
  border-radius: 8px;
  background: #fff;
  border: 1px solid #e5e7eb;
  cursor: pointer;
  transition: all 0.2s;
}

.quick-action-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  border-color: #2080f0;
}

.quick-action-card:active {
  transform: translateY(0);
}
</style>
