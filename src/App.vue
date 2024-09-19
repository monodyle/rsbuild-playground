<script setup>
import Content from './Content.vue'
</script>

<template>
  <main>
    <div class="container">
      <Suspense>
        <Content />
        <template #fallback>
          <div class="center">loading...</div>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<style scoped>
main {
  width: 100%;
  height: 100vh;
  display: grid;
  place-items: center;
}

.container {
  width: 100%;
  max-width: 640px;
  padding: 2rem;
}

.center {
  text-align: center;
}
</style>
