<template>
  <div class="button">
    <span>{{ content }}</span>
    <unicon v-if="iconName" :name="iconName" />
  </div>
</template>

<script>
export default {
  props: {
    content: String,
    iconName: String,
  },
};
</script>

<style lang="less" scoped>
@import "~@/assets/styles/global.less";
</style>
