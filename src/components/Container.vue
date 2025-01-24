<script setup lang="ts">
import { ref } from "vue";

defineProps<{ msg: string }>();

interface ICardData {}

const data = ref<{}[]>([]);

const onClick = async () => {
  const clipboardItems = await navigator.clipboard.read();
  // const contentText = await navigator.clipboard.readText();
  console.log(navigator.clipboard)
  console.log("clipboardItems", clipboardItems);
  // 默认只有一个 clipboardItem
  const items: any = [];
  for (const clipboardItem of clipboardItems) {
    const blobs: any = [];
    for (const type of clipboardItem.types) {
      const blob = await clipboardItem.getType(type);
      console.log(blob)
      const dataHtmlText = await blob.text();
      console.log(type, dataHtmlText);
      blobs.push({ type, dataHtmlText });
    }
    items.push(blobs);
  }
  data.value = items;
  console.log("data", data.value);
};

const onPaste = async (e)=>{
  console.log(e.clipboardData.files)
  console.log(e.clipboardData.items[0], e.clipboardData.items[1], e.clipboardData.items[2], e.clipboardData.items[3])
  console.log(e.clipboardData.types)
}

</script>

<template>
  <h1>{{ msg }}</h1>

  <div class="card">
    <button @click="onClick">点我看看</button>
    <input @paste="onPaste">
  </div>
  <div>
    <div v-for="item in data">
      <div v-for="i in item">
        <div>{{ i.type }}：{{ i.dataHtmlText }}</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
