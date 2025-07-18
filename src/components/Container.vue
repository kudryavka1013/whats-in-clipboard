<script setup lang="ts">
import { ref } from "vue";
import ClipboardItemCard from "./ClipboardItemCard.vue";

interface ICardData {
  type: string;
  mimeType: MIMEType | string;
  fileList?: { name: string; size: number }[];
  dataRawText?: string;
}

const enum MIMEType {
  TEXT = "text/plain",
  HTML = "text/html",
  IMAGE = "image/png",
  FILE = "Files",
}

const action = ref<boolean>(false);
const data = ref<ICardData[][]>([]);

const onPaste = async (e: ClipboardEvent) => {
  action.value = true;
  data.value = [];

  /** 当同时使用异步 navigator.clipboard.read() 和 event 时
   * 浏览器会优先处理异步剪贴板数据，部分浏览器会清空或锁定同步的 e.clipboardData
   * 导致在同一个 paste 事件里无法再通过 e.clipboardData 读取到原始数据
   */

  // Sync Event
  if (!e.clipboardData) return;
  const types = e.clipboardData.types;
  const infos: ICardData[] = [];
  for (const mimeType of types) {
    const dataRawText = e.clipboardData.getData(mimeType);
    if (mimeType !== MIMEType.FILE) {
      infos.push({ type: "Sync Event", mimeType, dataRawText });
    } else {
      if (e.clipboardData.files.length > 0) {
        const fileList: { name: string; size: number }[] = Array.from(
          e.clipboardData.files
        ).map((file: File) => ({
          name: file.name || "",
          size: file.size || 0,
        }));
        infos.push({
          type: "Sync Event",
          mimeType: MIMEType.FILE,
          fileList,
        });
      }
    }
  }
  data.value.push(infos);

  // Async API
  const clipboardItems = await navigator.clipboard.read();
  // 通常只有一个 clipboardItem
  const items: ICardData[][] = [];
  for (const clipboardItem of clipboardItems) {
    const blobs: ICardData[] = [];
    for (const mimeType of clipboardItem.types) {
      const blob = await clipboardItem.getType(mimeType);
      const dataRawText = await blob.text();

      if (mimeType === MIMEType.TEXT) {
        const existingPlainTextItem = data.value
          .flat()
          .find(
            (blob) =>
              blob.mimeType === MIMEType.TEXT &&
              blob.dataRawText === dataRawText
          );
        if (existingPlainTextItem) {
          existingPlainTextItem.type = "Sync Event / Async API";
          continue;
        }
      }

      blobs.push({ type: "Async API", mimeType, dataRawText });
    }
    items.push(blobs);
  }
  data.value.push(...items);
};

window.addEventListener("paste", onPaste);
</script>

<template>
  <div class="container">
    <p>Press<code>Ctrl/Cmd + V</code>to get data.</p>
    <div>
      <div v-for="(item, itemIdx) in data" :key="itemIdx">
        <div v-for="(blob, blobIdx) in item" :key="blobIdx">
          <ClipboardItemCard
            :type="blob.type"
            :mimeType="blob.mimeType"
            :content="blob.dataRawText"
            :fileList="blob.fileList"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
p {
  font-size: 1.2em;
}

code {
  margin-left: 0.8em;
  margin-right: 0.8em;
}

button {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.4em 0.8em;
  font-size: 1.2rem;
  cursor: pointer;
  transition: border-color 0.25s;
  border-color: #646cff80;
  margin-left: 0.4em;
  margin-right: 0.4em;
  color: #213547;
}

button:hover {
  border-color: #646cff;
}
</style>
