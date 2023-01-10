<template>
    <div class="loader"></div>
</template>

<style scoped>
  .loader {
    border: 16px solid #f3f3f3;
    border-radius: 50%;
    border-top: 16px solid #23241a;
    width: 150px;
    height: 150px;
    animation: spin 1s linear infinite;
  }
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
</style>

<script>
export default {
  name: "SpinnerLoader",
};
</script>