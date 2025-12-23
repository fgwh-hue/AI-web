<script setup lang="ts">
defineOptions({
  name: 'AdminCreativityBanner'
});
</script>

<template>
  <NCard title="系统概览" :bordered="false" size="small" class="h-full card-wrapper">
    <div class="h-full flex-center">
      <icon-local-banner class="text-400px text-primary sm:text-320px" />
    </div>
  </NCard>
</template>

<style scoped></style>
