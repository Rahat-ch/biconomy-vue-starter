<script setup lang="ts">

defineProps<{ msg: string, address: string }>()
console.log("from hello component")
</script>

<template>
  <h2 v-if="address" >{{ msg }}</h2>
  <p v-if="address">{{ address }}</p>
  <div class="card">
    <p class="read-the-docs">
    <a href="https://docs.biconomy.io/docs/overview" target="_blank">
      Click here to check out the Biconomy docs
    </a>
  </p>
  </div>

</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
