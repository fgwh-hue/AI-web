<script setup lang="ts">
import { useEcharts } from '@/hooks/common/echarts';

const { domRef } = useEcharts(() => ({
  title: {
    text: '本周考勤情况',
    left: 'center'
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'shadow'
    }
  },
  legend: {
    data: ['1-2节', '3-4节', '5-6节', '7-8节', '9-10节'],
    bottom: '2%'
  },
  grid: {
    top: '20%',
    left: '3%',
    right: '4%',
    bottom: '15%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
  },
  yAxis: {
    type: 'value',
    max: 4 // 调整Y轴最大值，让柱子看起来更高
  },
  series: [
    // 移除 stack 属性，让柱子并排显示
    { name: '1-2节', type: 'bar', data: [1, 2, 2, 1, 0, 2, 1] },
    { name: '3-4节', type: 'bar', data: [2, 1, 0, 2, 2, 1, 2] },
    { name: '5-6节', type: 'bar', data: [0, 0, 1.5, 0, 0, 0, 0] },
    { name: '7-8节', type: 'bar', data: [0, 0, 0, 0, 0, 0, 0] },
    { name: '9-10节', type: 'bar', data: [0, 0, 2, 0, 0, 0, 0] }
  ]
}));
</script>

<template>
  <div ref="domRef" class="h-full w-full"></div>
</template>
