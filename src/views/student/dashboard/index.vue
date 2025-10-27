<script setup lang="ts">
import SvgIcon from '@/components/custom/svg-icon.vue';
</script>

<template>
  <div>
    <!--
      这个隐藏的 div 是为了让打包工具能够扫描到我们需要的图标。
      因为菜单数据是在 mock 文件中动态加载的，所以打包工具无法静态分析到需要哪些图标。
      在这里显式引用一次后，图标就会被正确打包。
    -->
    <div class="hidden">
      <SvgIcon icon="mdi:home" />
      <SvgIcon icon="ph:robot" />
      <SvgIcon icon="mdi:chart-timeline-variant" />
    </div>

    <LookForward>
      <h2>个性化学习中心 - 正在开发中...</h2>
    </LookForward>
  </div>
</template>
