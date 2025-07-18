<script setup lang="ts">
import { ref, watch } from "vue";
import FoldUpOneIcon from "../assets/fold-up-one.svg";
import ExpandDownOneIcon from "../assets/expand-down-one.svg";

const props = defineProps<{
  type: string;
  mimeType: string;
  fileList?: { name: string; size: number }[];
  content?: string;
}>();
const isExpanded = ref(true);

const toggleExpanded = () => {
  isExpanded.value = !isExpanded.value;
};
// 监听 props 变化，数据更新时重新展开
watch(
  () => [props.type, props.mimeType, props.content],
  () => {
    isExpanded.value = true;
  }
);
</script>

<template>
  <div class="card">
    <div class="type-container">
      <div class="type-info">
        <span class="type">{{ props.type }}</span>
        <span class="mime-type">{{ props.mimeType }}</span>
      </div>
      <span class="toggle-icon" @click="toggleExpanded">
        <img :src="isExpanded ? FoldUpOneIcon : ExpandDownOneIcon" />
      </span>
    </div>
    <div class="content" :class="{ expanded: isExpanded }">
      <div class="scroll-inner">
        <code>
          {{
            props.fileList?.length
              ? props.fileList
                  .map(
                    (file) =>
                      `FileName: ${file.name}\nFileSize: ${file.size} bytes`
                  )
                  .join("\n\n")
              : props.content
          }}
        </code>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  border-bottom: 1px solid #e2e8f0;
  border-radius: 4px;
  margin-bottom: 1.5em;
  background-color: #f8fafc;
  width: 800px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.type-container {
  display: flex;
  justify-content: space-between;
  background-color: #3b82f6;
  color: #f8fafc;
  padding: 8px 12px;
  font-weight: 500;
  border-radius: 4px;
  position: sticky;
  top: 0;
}

.type-info {
  display: flex;
  gap: 12px;
  align-items: center;
}

.type {
  background-color: #8b5cf6;
  color: white;
  padding: 4px 6px;
  border-radius: 4px;
  font-size: 0.8em;
  font-weight: 500;
}

.toggle-icon {
  cursor: pointer;
  user-select: none;
}

.content {
  text-align: left;
  white-space: pre-wrap;
  padding: 0 4px;
  display: grid;
  grid-template-rows: 0fr;
  transition: all 0.3s ease;
  overflow: hidden;
  margin: 0 12px;
  border-radius: 4px;
  word-break: break-all;
}

.content.expanded {
  grid-template-rows: 1fr;
  padding: 4px;
  margin: 12px;
  border: 1px solid #ccc;
}

.scroll-inner {
  min-height: 0;
}

@media (prefers-color-scheme: dark) {
  .card {
    background-color: #1f2937;
    border-color: #374151;
  }

  .content {
    background-color: #111827;
    border-color: #374151;
    color: #e5e7eb;
  }

  .type {
    background-color: #4b5563;
  }
}
</style>
