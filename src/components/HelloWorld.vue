<script setup lang="ts">
// import { ref } from 'vue';


defineProps<{ msg: string }>();

// const data = ref(0)

const onClick = async () => {
  const clipboardItems = await navigator.clipboard.read();
  // const contentText = await navigator.clipboard.readText();
  console.log("clipboardItems", clipboardItems);
  for (const clipboardItem of clipboardItems) {
      for (const type of clipboardItem.types) {
        const blob = await clipboardItem.getType(type);
        const dataHtmlText = await blob.text();
        console.log(type,dataHtmlText)
      }
    }
  // console.log("text", contentText);
};
</script>

<template>
  <h1>{{ msg }}</h1>

  <div class="card">
    <button @click="onClick">点我看看</button>
  </div>

  <!-- <div>{{ data }}</div> -->
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
