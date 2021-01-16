<template>
  <div class="avatar">
    <img :src="data.value">
  </div>
</template>

<script>
export default {
  name: 'AppAvatar',
  props: ['data']
}
</script>

<style scoped>

</style>
