<script setup lang="ts">
import { computed } from 'vue';

defineOptions({
  name: 'AdminProjectNews'
});

interface NewsItem {
  id: number;
  content: string;
  time: string;
}

const newses = computed<NewsItem[]>(() => [
  { id: 1, content: '新增用户注册审核功能', time: '2025-12-10 10:00:00' },
  { id: 2, content: '系统安全补丁已更新', time: '2025-12-09 14:30:00' },
  { id: 3, content: '数据库备份完成', time: '2025-12-08 08:00:00' },
  { id: 4, content: '新增课程审核通过 5 门', time: '2025-12-07 16:20:00' },
  { id: 5, content: '系统性能优化完成', time: '2025-12-06 11:00:00' }
]);
</script>

<template>
  <NCard title="系统动态" :bordered="false" size="small" segmented class="card-wrapper">
    <template #header-extra>
      <a class="text-primary" href="javascript:;">查看更多</a>
    </template>
    <NList>
      <NListItem v-for="item in newses" :key="item.id">
        <template #prefix>
          <SoybeanAvatar class="size-48px!" />
        </template>
        <NThing :title="item.content" :description="item.time" />
      </NListItem>
    </NList>
  </NCard>
</template>

<style scoped></style>
