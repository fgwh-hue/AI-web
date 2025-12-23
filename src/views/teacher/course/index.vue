<script setup lang="ts">
import { computed, h, ref } from 'vue';
import { NButton, NPopconfirm, NProgress, NSpace } from 'naive-ui';
import { useAppStore } from '@/store/modules/app';

defineOptions({
  name: 'TeacherCourse'
});

const appStore = useAppStore();
const gap = computed(() => (appStore.isMobile ? 0 : 16));

interface Course {
  id: string;
  name: string;
  code: string;
  students: number;
  status: 'active' | 'draft' | 'archived';
  createdAt: string;
  description?: string;
  schedule?: string;
  assignments?: number;
  avgScore?: number;
  category?: string;
}

const courses = ref<Course[]>([
  {
    id: '1',
    name: '高等数学',
    code: 'MATH101',
    students: 45,
    status: 'active',
    createdAt: '2024-01-15',
    description: '微积分与线性代数基础',
    schedule: '周一、周三 14:00-16:00',
    assignments: 12,
    avgScore: 82,
    category: '数学'
  },
  {
    id: '2',
    name: '英语写作',
    code: 'ENG201',
    students: 38,
    status: 'active',
    createdAt: '2024-01-20',
    description: '学术英语写作技巧',
    schedule: '周二、周四 10:00-12:00',
    assignments: 8,
    avgScore: 85,
    category: '语言'
  },
  {
    id: '3',
    name: '物理实验',
    code: 'PHY301',
    students: 42,
    status: 'active',
    createdAt: '2024-02-01',
    description: '基础物理实验操作',
    schedule: '周五 14:00-17:00',
    assignments: 10,
    avgScore: 79,
    category: '理科'
  },
  {
    id: '4',
    name: '化学基础',
    code: 'CHEM101',
    students: 31,
    status: 'draft',
    createdAt: '2024-02-10',
    description: '化学基本原理与应用',
    schedule: '待定',
    assignments: 0,
    avgScore: 0,
    category: '理科'
  },
  {
    id: '5',
    name: '计算机编程',
    code: 'CS101',
    students: 52,
    status: 'active',
    createdAt: '2024-02-15',
    description: 'Python编程基础与算法',
    schedule: '周一、周三 10:00-12:00',
    assignments: 15,
    avgScore: 88,
    category: '理科'
  },
  {
    id: '6',
    name: '中国文学',
    code: 'LIT201',
    students: 35,
    status: 'active',
    createdAt: '2024-02-20',
    description: '古代文学作品赏析',
    schedule: '周二、周四 14:00-16:00',
    assignments: 6,
    avgScore: 86,
    category: '文科'
  }
]);

// 获取课程名称的第一个字
function getCourseFirstChar(name: string): string {
  return name.charAt(0);
}

// 生成渐变背景类名
function getGradientClass(index: number): string {
  const gradients = [
    'gradient-primary',
    'gradient-success',
    'gradient-warning',
    'gradient-info',
    'gradient-purple',
    'gradient-orange'
  ];
  return gradients[index % gradients.length];
}

const showModal = ref(false);
const editingCourse = ref<Course | null>(null);
const showCategoryDetailModal = ref(false);
const selectedCategoryDetail = ref<any>(null);
const searchText = ref('');
const selectedStatus = ref<string | null>(null);
const selectedCategory = ref<string | null>(null);
const viewMode = ref<'table' | 'card'>('card');

const filteredCourses = computed(() => {
  return courses.value.filter(course => {
    const matchSearch =
      !searchText.value || course.name.includes(searchText.value) || course.code.includes(searchText.value);
    const matchStatus = !selectedStatus.value || course.status === selectedStatus.value;
    const matchCategory = !selectedCategory.value || course.category === selectedCategory.value;
    return matchSearch && matchStatus && matchCategory;
  });
});

const courseStats = computed(() => ({
  total: courses.value.length,
  active: courses.value.filter(c => c.status === 'active').length,
  draft: courses.value.filter(c => c.status === 'draft').length,
  totalStudents: courses.value.reduce((sum, c) => sum + c.students, 0),
  avgStudents: Math.round(courses.value.reduce((sum, c) => sum + c.students, 0) / courses.value.length),
  totalAssignments: courses.value.reduce((sum, c) => sum + (c.assignments || 0), 0)
}));

// 课程分类统计（丰富数据）
const categoryStats = computed(() => {
  const stats: Record<string, { count: number; students: number; avgScore: number; assignments: number }> = {};

  courses.value.forEach(course => {
    const cat = course.category || '其他';
    if (!stats[cat]) {
      stats[cat] = { count: 0, students: 0, avgScore: 0, assignments: 0 };
    }
    stats[cat].count += 1;
    stats[cat].students += course.students;
    stats[cat].avgScore += course.avgScore || 0;
    stats[cat].assignments += course.assignments || 0;
  });

  return Object.entries(stats).map(([name, data]) => ({
    name,
    value: data.count,
    students: data.students,
    avgScore: Math.round(data.avgScore / data.count) || 0,
    assignments: data.assignments
  }));
});

// 最近活跃课程
const recentActiveCourses = computed(() => {
  return courses.value
    .filter(c => c.status === 'active')
    .sort((a, b) => new Date(b.createdAt).getTime() - new Date(a.createdAt).getTime())
    .slice(0, 5);
});

// 课程完成度数据（模拟）
const courseProgress = computed(() => {
  return courses.value
    .filter(c => c.status === 'active')
    .map(course => ({
      name: course.name,
      progress: Math.round((course.assignments || 0) * 6.67), // 假设15个作业为100%
      assignments: course.assignments || 0
    }));
});

// 周统计数据（模拟）
const weeklyTrend = [
  { day: '周一', students: 120, assignments: 8 },
  { day: '周二', students: 135, assignments: 12 },
  { day: '周三', students: 142, assignments: 15 },
  { day: '周四', students: 138, assignments: 10 },
  { day: '周五', students: 145, assignments: 14 },
  { day: '周六', students: 98, assignments: 6 },
  { day: '周日', students: 85, assignments: 4 }
];

const statusOptions = [
  { label: '进行中', value: 'active' },
  { label: '草稿', value: 'draft' },
  { label: '已归档', value: 'archived' }
];

const categoryOptions = [
  { label: '数学', value: '数学' },
  { label: '语言', value: '语言' },
  { label: '理科', value: '理科' },
  { label: '文科', value: '文科' }
];

function handleAdd() {
  editingCourse.value = {
    id: '',
    name: '',
    code: '',
    students: 0,
    status: 'draft',
    createdAt: new Date().toISOString().split('T')[0],
    description: '',
    schedule: '',
    assignments: 0,
    avgScore: 0,
    category: '数学'
  };
  showModal.value = true;
}

function handleEdit(course: Course) {
  editingCourse.value = { ...course };
  showModal.value = true;
}

function handleDelete(id: string) {
  const index = courses.value.findIndex(c => c.id === id);
  if (index > -1) {
    courses.value.splice(index, 1);
    window.$message?.success('删除成功');
  }
}

function handleSave() {
  if (editingCourse.value) {
    if (editingCourse.value.id) {
      const index = courses.value.findIndex(c => c.id === editingCourse.value!.id);
      if (index > -1) {
        courses.value[index] = editingCourse.value;
      }
    } else {
      editingCourse.value.id = Date.now().toString();
      courses.value.push(editingCourse.value);
    }
    window.$message?.success('保存成功');
  }
  showModal.value = false;
}

function handleCategoryClick(category: any) {
  selectedCategoryDetail.value = category;
  showCategoryDetailModal.value = true;
}

function getStatusLabel(status: string) {
  const option = statusOptions.find(o => o.value === status);
  return option?.label || status;
}

function getStatusType(status: string) {
  const typeMap: Record<string, 'success' | 'warning' | 'default'> = {
    active: 'success',
    draft: 'warning',
    archived: 'default'
  };
  return typeMap[status] || 'default';
}

function getScoreColorClass(score: number) {
  if (score >= 80) return 'text-success';
  if (score >= 60) return 'text-warning';
  return 'text-error';
}

const tableColumns = computed(() => [
  { title: '课程名称', key: 'name', width: 150 },
  { title: '课程代码', key: 'code', width: 120 },
  { title: '分类', key: 'category', width: 100 },
  { title: '学生数', key: 'students', width: 100 },
  { title: '作业数', key: 'assignments', width: 100 },
  { title: '平均分', key: 'avgScore', width: 100 },
  { title: '创建时间', key: 'createdAt', width: 120 },
  {
    title: '操作',
    key: 'actions',
    width: 150,
    fixed: 'right' as const,
    render: (row: Course) => {
      return h(
        NSpace,
        { size: 'small' },
        {
          default: () => [
            h(
              NButton,
              {
                size: 'small',
                onClick: () => handleEdit(row)
              },
              { default: () => '编辑' }
            ),
            h(
              NPopconfirm,
              {
                onPositiveClick: () => handleDelete(row.id)
              },
              {
                default: () => '确定要删除这个课程吗？',
                trigger: () =>
                  h(
                    NButton,
                    {
                      size: 'small',
                      type: 'error'
                    },
                    { default: () => '删除' }
                  )
              }
            )
          ]
        }
      );
    }
  }
]);
</script>

<template>
  <NSpace vertical :size="16">
    <!-- Header -->
    <div class="flex-y-center justify-between">
      <h2 class="text-20px font-semibold">课程管理</h2>
      <NButton type="primary" @click="handleAdd">
        <template #icon>
          <div class="i-mdi-plus" />
        </template>
        新增课程
      </NButton>
    </div>

    <!-- Search and Filter -->
    <NCard :bordered="false" class="card-wrapper">
      <div class="flex-y-center justify-between">
        <NSpace>
          <NInput v-model:value="searchText" placeholder="搜索课程名称或代码" class="w-200px" clearable>
            <template #prefix>
              <div class="i-mdi-magnify" />
            </template>
          </NInput>
          <NSelect
            v-model:value="selectedCategory"
            placeholder="选择分类"
            class="w-120px"
            :options="categoryOptions"
            clearable
          />
          <NSelect
            v-model:value="selectedStatus"
            placeholder="选择状态"
            class="w-120px"
            :options="statusOptions"
            clearable
          />
          <NButton
            @click="
              () => {
                searchText = '';
                selectedStatus = null;
                selectedCategory = null;
              }
            "
          >
            重置
          </NButton>
        </NSpace>
        <NButtonGroup>
          <NButton :type="viewMode === 'table' ? 'primary' : 'default'" @click="viewMode = 'table'">
            <template #icon>
              <div class="i-mdi-table" />
            </template>
          </NButton>
          <NButton :type="viewMode === 'card' ? 'primary' : 'default'" @click="viewMode = 'card'">
            <template #icon>
              <div class="i-mdi-view-grid" />
            </template>
          </NButton>
        </NButtonGroup>
      </div>
    </NCard>

    <!-- Statistics Cards with Icons -->
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <NGi span="24 s:12 m:8 l:4">
        <NCard :bordered="false" class="stat-card stat-card-primary card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">{{ courseStats.total }}</div>
              <div class="mt-8px text-14px opacity-80">总课程数</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-book-multiple text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:8 l:4">
        <NCard :bordered="false" class="stat-card stat-card-success card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">{{ courseStats.active }}</div>
              <div class="mt-8px text-14px opacity-80">进行中</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-play-circle text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:8 l:4">
        <NCard :bordered="false" class="stat-card stat-card-warning card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">{{ courseStats.draft }}</div>
              <div class="mt-8px text-14px opacity-80">草稿</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-file-document-edit text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:8 l:4">
        <NCard :bordered="false" class="stat-card stat-card-info card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">{{ courseStats.totalStudents }}</div>
              <div class="mt-8px text-14px opacity-80">总学生数</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-account-group text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:8 l:4">
        <NCard :bordered="false" class="stat-card stat-card-purple card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">{{ courseStats.avgStudents }}</div>
              <div class="mt-8px text-14px opacity-80">平均学生数</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-account-multiple text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
      <NGi span="24 s:12 m:8 l:4">
        <NCard :bordered="false" class="stat-card stat-card-orange card-wrapper">
          <div class="flex-y-center justify-between">
            <div>
              <div class="text-32px font-bold">{{ courseStats.totalAssignments }}</div>
              <div class="mt-8px text-14px opacity-80">总作业数</div>
            </div>
            <div class="size-56px flex-center rd-12px bg-white bg-op-20">
              <div class="i-mdi-clipboard-list text-32px" />
            </div>
          </div>
        </NCard>
      </NGi>
    </NGrid>

    <!-- Charts and Analytics -->
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <!-- Weekly Trend Chart -->
      <NGi span="24 s:24 m:16">
        <NCard :bordered="false" class="card-wrapper">
          <template #header>
            <div class="flex-y-center justify-between">
              <span class="font-semibold">本周活动趋势</span>
              <NSpace>
                <div class="flex-y-center gap-8px">
                  <div class="size-12px rd-1/2 bg-primary" />
                  <span class="text-12px">学生活跃度</span>
                </div>
                <div class="flex-y-center gap-8px">
                  <div class="size-12px rd-1/2 bg-success" />
                  <span class="text-12px">作业提交</span>
                </div>
              </NSpace>
            </div>
          </template>
          <div class="h-280px">
            <NSpace vertical :size="8" class="h-full">
              <div v-for="item in weeklyTrend" :key="item.day" class="flex-y-center gap-12px">
                <span class="w-48px text-12px">{{ item.day }}</span>
                <div class="flex-1">
                  <div class="mb-4px flex-y-center gap-8px">
                    <div
                      class="h-20px rd-4px bg-primary transition-all"
                      :style="{ width: `${(item.students / 150) * 100}%` }"
                    />
                    <span class="text-12px text-#999">{{ item.students }}</span>
                  </div>
                  <div class="flex-y-center gap-8px">
                    <div
                      class="h-20px rd-4px bg-success transition-all"
                      :style="{ width: `${(item.assignments / 15) * 100}%` }"
                    />
                    <span class="text-12px text-#999">{{ item.assignments }}</span>
                  </div>
                </div>
              </div>
            </NSpace>
          </div>
        </NCard>
      </NGi>

      <!-- Category Distribution -->
      <NGi span="24 s:24 m:8">
        <NCard :bordered="false" class="card-wrapper">
          <template #header>
            <span class="font-semibold">课程分类分布</span>
          </template>
          <div class="h-280px flex-col gap-16px p-16px">
            <!-- Enhanced Pie Chart -->
            <div class="flex-center flex-1">
              <div class="relative size-180px">
                <!-- Pie Chart Background -->
                <div class="absolute inset-0 border-16px border-gray-200 rd-1/2"></div>

                <!-- Pie Chart Segments -->
                <div
                  v-for="(cat, idx) in categoryStats"
                  :key="cat.name"
                  class="absolute inset-0 rd-1/2"
                  :style="{
                    transform: `rotate(${idx * (360 / categoryStats.length)}deg)`,
                    clipPath: `polygon(50% 50%, 50% 0%, calc(50% + 50% * cos(${360 / categoryStats.length}deg)) calc(50% + 50% * sin(${360 / categoryStats.length}deg)), 50% 50%)`
                  }"
                >
                  <div
                    class="absolute inset-0 rd-1/2 transition-all hover:scale-105"
                    :class="{
                      'bg-primary': idx === 0,
                      'bg-success': idx === 1,
                      'bg-warning': idx === 2,
                      'bg-info': idx === 3,
                      'bg-purple': idx === 4,
                      'bg-orange': idx === 5
                    }"
                    :style="{ transform: `rotate(-${idx * (360 / categoryStats.length)}deg)` }"
                  ></div>
                </div>

                <!-- Center Circle -->
                <div class="absolute inset-1/4 flex-center flex-col rd-1/2 bg-white shadow-sm">
                  <div class="text-24px font-bold">{{ categoryStats.length }}</div>
                  <div class="text-12px text-#999">分类</div>
                </div>
              </div>
            </div>

            <!-- Enhanced Legend with Detailed Stats -->
            <div class="flex-col flex-1 gap-12px overflow-auto">
              <div
                v-for="(cat, idx) in categoryStats"
                :key="cat.name"
                class="flex-col cursor-pointer gap-6px rd-8px bg-gray-50 p-12px transition-all hover:bg-gray-100"
                @click="handleCategoryClick(cat)"
              >
                <div class="flex-y-center justify-between">
                  <div class="flex-y-center gap-10px">
                    <div
                      class="size-12px rd-1/2"
                      :class="{
                        'bg-primary': idx === 0,
                        'bg-success': idx === 1,
                        'bg-warning': idx === 2,
                        'bg-info': idx === 3,
                        'bg-purple': idx === 4,
                        'bg-orange': idx === 5
                      }"
                    />
                    <div class="text-14px font-semibold">{{ cat.name }}</div>
                  </div>
                  <div
                    class="text-16px font-bold"
                    :class="{
                      'text-primary': idx === 0,
                      'text-success': idx === 1,
                      'text-warning': idx === 2,
                      'text-info': idx === 3,
                      'text-purple': idx === 4,
                      'text-orange': idx === 5
                    }"
                  >
                    {{ cat.value }}门
                  </div>
                </div>

                <!-- Detailed Stats -->
                <div class="grid grid-cols-3 gap-8px">
                  <div class="flex-col gap-2px">
                    <div class="text-10px text-#999">学生总数</div>
                    <div class="text-12px font-bold">{{ cat.students }}</div>
                  </div>
                  <div class="flex-col gap-2px">
                    <div class="text-10px text-#999">平均分数</div>
                    <div class="text-12px font-bold" :class="getScoreColorClass(cat.avgScore)">
                      {{ cat.avgScore || '-' }}
                    </div>
                  </div>
                  <div class="flex-col gap-2px">
                    <div class="text-10px text-#999">作业总数</div>
                    <div class="text-12px font-bold">{{ cat.assignments }}</div>
                  </div>
                </div>

                <!-- Progress Bar for Course Count -->
                <div class="w-full">
                  <NProgress
                    type="line"
                    :percentage="Math.round((cat.value / courseStats.total) * 100)"
                    :color="
                      idx === 0
                        ? '#667eea'
                        : idx === 1
                          ? '#18a058'
                          : idx === 2
                            ? '#f0a020'
                            : idx === 3
                              ? '#2080f0'
                              : idx === 4
                                ? '#9333ea'
                                : '#f97316'
                    "
                    :show-indicator="false"
                  />
                </div>
              </div>
            </div>
          </div>
        </NCard>
      </NGi>
    </NGrid>

    <!-- Course Progress & Recent Activity -->
    <NGrid :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <!-- Course Progress -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="h-full card-wrapper">
          <template #header>
            <div class="flex-y-center justify-between">
              <span class="font-semibold">课程进度</span>
              <NButton text type="primary" size="small">查看全部</NButton>
            </div>
          </template>
          <div class="min-h-320px">
            <NSpace vertical :size="16">
              <div v-for="item in courseProgress.slice(0, 5)" :key="item.name" class="flex-col gap-8px">
                <div class="flex-y-center justify-between">
                  <span class="text-14px">{{ item.name }}</span>
                  <span class="text-12px text-#999">{{ item.assignments }}/15 作业</span>
                </div>
                <NProgress
                  type="line"
                  :percentage="item.progress"
                  :color="item.progress >= 80 ? '#18a058' : item.progress >= 50 ? '#f0a020' : '#d03050'"
                  :show-indicator="false"
                />
              </div>
            </NSpace>
          </div>
        </NCard>
      </NGi>

      <!-- Recent Active Courses -->
      <NGi span="24 s:24 m:12">
        <NCard :bordered="false" class="h-full card-wrapper">
          <template #header>
            <div class="flex-y-center justify-between">
              <span class="font-semibold">最近活跃课程</span>
              <NButton text type="primary" size="small">查看全部</NButton>
            </div>
          </template>
          <div class="min-h-320px">
            <NSpace vertical :size="12">
              <div
                v-for="(course, index) in recentActiveCourses"
                :key="course.id"
                class="flex-y-center justify-between border-b pb-12px"
              >
                <div class="flex-y-center gap-12px">
                  <div class="size-40px flex-center rd-8px text-white font-bold" :class="getGradientClass(index)">
                    {{ getCourseFirstChar(course.name) }}
                  </div>
                  <div>
                    <div class="text-14px font-semibold">{{ course.name }}</div>
                    <div class="text-12px text-#999">{{ course.students }} 名学生</div>
                  </div>
                </div>
                <div class="text-right">
                  <div class="text-14px font-bold" :class="getScoreColorClass(course.avgScore || 0)">
                    {{ course.avgScore || '-' }}
                  </div>
                  <div class="text-12px text-#999">平均分</div>
                </div>
              </div>
            </NSpace>
          </div>
        </NCard>
      </NGi>
    </NGrid>

    <!-- Quick Actions -->
    <NCard :bordered="false" class="card-wrapper">
      <template #header>
        <span class="font-semibold">快速操作</span>
      </template>
      <NSpace class="quick-actions-buttons">
        <NButton type="primary" class="quick-action-btn" @click="handleAdd">
          <template #icon>
            <div class="i-mdi-plus" />
          </template>
          新增课程
        </NButton>
        <NButton class="quick-action-btn">
          <template #icon>
            <div class="i-mdi-clipboard-list" />
          </template>
          发布作业
        </NButton>
        <NButton class="quick-action-btn">
          <template #icon>
            <div class="i-mdi-chart-line" />
          </template>
          查看报告
        </NButton>
        <NButton class="quick-action-btn">
          <template #icon>
            <div class="i-mdi-download" />
          </template>
          导出数据
        </NButton>
        <NButton class="quick-action-btn">
          <template #icon>
            <div class="i-mdi-cog" />
          </template>
          课程设置
        </NButton>
      </NSpace>
    </NCard>

    <!-- Card View -->
    <NGrid v-if="viewMode === 'card'" :x-gap="gap" :y-gap="16" responsive="screen" item-responsive>
      <NGi v-for="(course, index) in filteredCourses" :key="course.id" span="24 s:24 m:12 l:8">
        <NCard :bordered="false" class="cursor-pointer card-wrapper hover-shadow" @click="handleEdit(course)">
          <div class="flex-col gap-16px">
            <!-- Header -->
            <div class="flex-y-center justify-between">
              <div class="flex-y-center gap-12px">
                <div class="size-48px flex-center rd-12px text-white font-bold" :class="getGradientClass(index)">
                  {{ getCourseFirstChar(course.name) }}
                </div>
                <div>
                  <div class="text-16px font-bold">{{ course.name }}</div>
                  <div class="text-12px text-#999">{{ course.code }}</div>
                </div>
              </div>
              <div class="flex-y-center gap-8px">
                <NBadge :value="getStatusLabel(course.status)" :type="getStatusType(course.status)" />
                <NPopconfirm @positive-click="handleDelete(course.id)">
                  <template #trigger>
                    <NButton size="small" type="error" text @click.stop>删除</NButton>
                  </template>
                  确定要删除这个课程吗？
                </NPopconfirm>
              </div>
            </div>

            <!-- Description -->
            <div class="line-clamp-2 text-14px text-#666">
              {{ course.description || '暂无描述' }}
            </div>

            <!-- Stats -->
            <div class="flex gap-16px">
              <div class="flex-1 rd-8px bg-#f5f5f5 p-8px text-center">
                <div class="text-18px text-primary font-bold">{{ course.students }}</div>
                <div class="text-12px text-#999">学生</div>
              </div>
              <div class="flex-1 rd-8px bg-#f5f5f5 p-8px text-center">
                <div class="text-18px text-warning font-bold">{{ course.assignments || 0 }}</div>
                <div class="text-12px text-#999">作业</div>
              </div>
              <div class="flex-1 rd-8px bg-#f5f5f5 p-8px text-center">
                <div class="text-18px font-bold" :class="getScoreColorClass(course.avgScore || 0)">
                  {{ course.avgScore || '-' }}
                </div>
                <div class="text-12px text-#999">平均分</div>
              </div>
            </div>

            <!-- Schedule -->
            <div class="flex-y-center gap-8px text-12px text-#666">
              <div class="i-mdi-clock-outline" />
              <span>{{ course.schedule || '暂无安排' }}</span>
            </div>
          </div>
        </NCard>
      </NGi>
    </NGrid>

    <!-- Table View -->
    <NCard v-else :bordered="false" class="card-wrapper">
      <NDataTable :columns="tableColumns" :data="filteredCourses" :pagination="{ pageSize: 10 }" :scroll-x="1000" />
    </NCard>

    <!-- Modal -->
    <NModal v-model:show="showModal" preset="card" title="课程信息" class="w-600px">
      <NForm v-if="editingCourse" label-placement="left" :label-width="80">
        <NFormItem label="课程名称" required>
          <NInput v-model:value="editingCourse.name" placeholder="请输入课程名称" />
        </NFormItem>
        <NFormItem label="课程代码" required>
          <NInput v-model:value="editingCourse.code" placeholder="请输入课程代码" />
        </NFormItem>
        <NFormItem label="分类">
          <NSelect v-model:value="editingCourse.category" :options="categoryOptions" />
        </NFormItem>
        <NFormItem label="状态">
          <NSelect v-model:value="editingCourse.status" :options="statusOptions" />
        </NFormItem>
        <NFormItem label="课程描述">
          <NInput v-model:value="editingCourse.description" type="textarea" placeholder="请输入课程描述" :rows="3" />
        </NFormItem>
        <NFormItem label="上课时间">
          <NInput v-model:value="editingCourse.schedule" placeholder="例如：周一、周三 14:00-16:00" />
        </NFormItem>
      </NForm>
      <template #footer>
        <div class="flex justify-end gap-8px">
          <NButton @click="showModal = false">取消</NButton>
          <NButton type="primary" @click="handleSave">保存</NButton>
        </div>
      </template>
    </NModal>

    <!-- Category Detail Modal -->
    <NModal v-model:show="showCategoryDetailModal" preset="card" title="分类详情" class="w-600px">
      <div v-if="selectedCategoryDetail" class="space-y-12px">
        <div class="flex items-center gap-12px">
          <div
            class="size-16px rd-1/2"
            :class="{
              'bg-primary': selectedCategoryDetail.name === (categoryStats[0]?.name || ''),
              'bg-success': selectedCategoryDetail.name === (categoryStats[1]?.name || ''),
              'bg-warning': selectedCategoryDetail.name === (categoryStats[2]?.name || ''),
              'bg-info': selectedCategoryDetail.name === (categoryStats[3]?.name || ''),
              'bg-purple': selectedCategoryDetail.name === (categoryStats[4]?.name || ''),
              'bg-orange': selectedCategoryDetail.name === (categoryStats[5]?.name || '')
            }"
          ></div>
          <h2 class="text-20px font-bold">{{ selectedCategoryDetail.name }}</h2>
        </div>
        <div class="grid grid-cols-2 gap-20px">
          <div class="rd-8px bg-gray-50 p-16px">
            <div class="mb-4px text-14px text-gray-500">课程数量</div>
            <div class="text-32px font-bold">{{ selectedCategoryDetail.value }}门</div>
          </div>
          <div class="rd-8px bg-gray-50 p-16px">
            <div class="mb-4px text-14px text-gray-500">学生总数</div>
            <div class="text-32px font-bold">{{ selectedCategoryDetail.students }}人</div>
          </div>
        </div>
        <div class="grid grid-cols-2 gap-20px">
          <div class="rd-8px bg-gray-50 p-16px">
            <div class="mb-4px text-14px text-gray-500">平均分数</div>
            <div class="text-32px font-bold">{{ selectedCategoryDetail.avgScore }}</div>
          </div>
          <div class="rd-8px bg-gray-50 p-16px">
            <div class="mb-4px text-14px text-gray-500">作业总数</div>
            <div class="text-32px font-bold">{{ selectedCategoryDetail.assignments }}</div>
          </div>
        </div>
      </div>
      <template #footer>
        <div class="flex justify-end gap-8px">
          <NButton @click="showCategoryDetailModal = false">关闭</NButton>
        </div>
      </template>
    </NModal>
  </NSpace>
</template>

<style scoped>
.hover-shadow {
  transition: box-shadow 0.3s;
}

.hover-shadow:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

/* 快速操作按钮样式 */
.quick-actions-buttons {
  width: 100%;
}

.quick-action-btn {
  flex: 1;
  text-align: center;
  justify-content: center;
}

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

.stat-card-purple {
  background: linear-gradient(135deg, #9333ea 0%, #7e22ce 100%);
}

.stat-card-orange {
  background: linear-gradient(135deg, #f97316 0%, #ea580c 100%);
}

/* Gradient classes for avatars */
.gradient-primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.gradient-success {
  background: linear-gradient(135deg, #18a058 0%, #0e7e3e 100%);
}

.gradient-warning {
  background: linear-gradient(135deg, #f0a020 0%, #d48806 100%);
}

.gradient-info {
  background: linear-gradient(135deg, #2080f0 0%, #1060c9 100%);
}

.gradient-purple {
  background: linear-gradient(135deg, #9333ea 0%, #7e22ce 100%);
}

.gradient-orange {
  background: linear-gradient(135deg, #f97316 0%, #ea580c 100%);
}
</style>
