<script setup lang="ts">
import { ref, watch } from "vue";
import UpIcon from "../assets/up.svg";
import DownIcon from "../assets/down.svg";

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
        <UpIcon v-if="isExpanded" />
        <DownIcon v-else />
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
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
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
    background-color: #1e293b;
    border-color: #334155;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.3);
  }

  .type-container {
    background-color: #1e40af;
    color: #f1f5f9;
  }

  .type {
    background-color: #7c3aed;
    color: #f1f5f9;
  }

  .mime-type {
    color: #cbd5e1;
  }

  .content {
    background-color: #0f172a;
    border-color: #334155;
  }

  .content.expanded {
    border-color: #475569;
  }

  code {
    color: #e2e8f0;
  }

  .toggle-icon {
    color: #f1f5f9;
  }

  .toggle-icon:hover {
    color: #cbd5e1;
  }
}
</style>
