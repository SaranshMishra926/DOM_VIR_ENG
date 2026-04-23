<template>
  <div class="post-card">
    <div class="left">
      <span class="index">#{{ index + 1 }}</span>
      <span class="dot" aria-hidden="true" />
      <span class="label">{{ item.label }}</span>
    </div>
    <span class="chip">{{ chip }}</span>
  </div>
</template>

<script>
export default {
  name: 'PostCard',
  props: {
    item: {
      type: Object,
      required: true,
    },
    index: {
      type: Number,
      required: true,
    },
    chip: {
      type: String,
      default: 'Live',
    },
  },
};
</script>

<style scoped>
.post-card {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  height: 44px;
  padding: 0 14px;
  border-bottom: 1px solid rgba(148, 163, 184, 0.12);
  box-sizing: border-box;
  color: #e5e7eb;
  transition: background-color 0.2s ease;
}

.post-card:hover {
  background: rgba(99, 102, 241, 0.08);
}

.left {
  display: flex;
  align-items: center;
  min-width: 0;
  gap: 10px;
}

.index {
  color: #64748b;
  min-width: 52px;
  font-size: 12px;
  font-weight: 600;
}

.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: linear-gradient(135deg, #818cf8, #ec4899);
  box-shadow: 0 0 12px rgba(129, 140, 248, 0.6);
}

.label {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  font-size: 13px;
  color: #e2e8f0;
}

.chip {
  border: 1px solid rgba(129, 140, 248, 0.3);
  border-radius: 999px;
  padding: 4px 9px;
  color: #c7d2fe;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  background: rgba(99, 102, 241, 0.18);
}
</style>
