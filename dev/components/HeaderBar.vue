<template>
  <header class="header-bar">
    <div>
      <p class="eyebrow">Virtualization Performance Demo</p>
      <h1>{{ title }}</h1>
      <p class="subtext">{{ subtitle }}</p>
    </div>
    <div class="status-pill">
      <span class="pulse" aria-hidden="true" />
      Stable 60Hz
    </div>
  </header>
</template>

<script>
export default {
  name: 'HeaderBar',
  props: {
    title: {
      type: String,
      default: 'SaaS Rendering Dashboard',
    },
    subtitle: {
      type: String,
      default: 'Compare normal and virtualized rendering in real time.',
    },
  },
};
</script>

<style scoped>
.header-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 14px;
}

.eyebrow {
  margin: 0;
  color: #93c5fd;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  font-size: 11px;
  font-weight: 700;
}

h1 {
  margin: 7px 0 6px;
  color: #f8fafc;
  font-size: 30px;
  font-weight: 800;
  letter-spacing: -0.02em;
}

.subtext {
  margin: 0;
  color: #94a3b8;
  font-size: 14px;
  line-height: 1.5;
}

.status-pill {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 13px;
  border-radius: 999px;
  border: 1px solid rgba(74, 222, 128, 0.4);
  background: rgba(15, 23, 42, 0.62);
  color: #bbf7d0;
  font-size: 12px;
  font-weight: 600;
  white-space: nowrap;
  box-shadow: 0 0 16px rgba(74, 222, 128, 0.12);
}

.pulse {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #4ade80;
  box-shadow: 0 0 10px rgba(74, 222, 128, 0.8);
  animation: pulse 1.6s ease infinite;
}

@keyframes pulse {
  0%,
  100% { opacity: 0.65; transform: scale(0.9); }
  50% { opacity: 1; transform: scale(1.08); }
}
</style>
