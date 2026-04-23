<template>
  <article class="metric-card">
    <div class="metric-label">
      <span class="icon" aria-hidden="true">{{ icon }}</span>
      <span>{{ label }}</span>
    </div>
    <div class="metric-value" :class="toneClass">{{ value }}</div>
    <div v-if="hint" class="metric-hint">{{ hint }}</div>
  </article>
</template>

<script>
export default {
  name: 'MetricCard',
  props: {
    label: {
      type: String,
      required: true,
    },
    value: {
      type: [String, Number],
      required: true,
    },
    hint: {
      type: String,
      default: '',
    },
    icon: {
      type: String,
      default: 'MET',
    },
    tone: {
      type: String,
      default: 'default',
    },
  },
  computed: {
    toneClass() {
      if (this.tone === 'success') return 'is-success';
      if (this.tone === 'danger') return 'is-danger';
      return '';
    },
  },
};
</script>

<style scoped>
.metric-card {
  position: relative;
  padding: 14px 14px 13px;
  border-radius: 14px;
  border: 1px solid rgba(148, 163, 184, 0.22);
  background: linear-gradient(160deg, rgba(30, 41, 59, 0.52), rgba(15, 23, 42, 0.35));
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.05);
  transition: transform 0.2s ease, border-color 0.2s ease, box-shadow 0.2s ease;
}

.metric-card:hover {
  transform: translateY(-1px);
  border-color: rgba(129, 140, 248, 0.4);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.07), 0 0 16px rgba(99, 102, 241, 0.2);
}

.metric-label {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #c7d2fe;
  font-size: 11px;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  font-weight: 600;
}

.icon {
  opacity: 0.78;
  color: #a5b4fc;
}

.metric-value {
  margin-top: 7px;
  color: #ffffff;
  font-size: 30px;
  font-weight: 800;
  line-height: 1.05;
  letter-spacing: -0.02em;
}

.metric-value.is-success {
  color: #4ade80;
}

.metric-value.is-danger {
  color: #f87171;
}

.metric-hint {
  margin-top: 5px;
  color: #94a3b8;
  font-size: 11px;
}
</style>
