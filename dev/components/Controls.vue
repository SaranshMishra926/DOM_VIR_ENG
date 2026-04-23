<template>
  <div class="controls">
    <button class="btn" type="button" @click="$emit('smaller-ahead')">Render Ahead -</button>
    <button class="btn" type="button" @click="$emit('larger-ahead')">Render Ahead +</button>
    <button class="btn ghost" type="button" @click="$emit('toggle-speed')">
      {{ speedLabel }}
    </button>
  </div>
</template>

<script>
export default {
  name: 'Controls',
  props: {
    speedLabel: {
      type: String,
      default: 'Normal Stream',
    },
  },
};
</script>

<style scoped>
.controls {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}

.btn {
  border: 1px solid rgba(255, 255, 255, 0.12);
  border-radius: 12px;
  padding: 10px 15px;
  color: #ffffff;
  font-weight: 600;
  font-size: 13px;
  cursor: pointer;
  background: linear-gradient(to right, #6366f1, #a855f7);
  box-shadow: 0 0 20px rgba(99, 102, 241, 0.28);
  transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease, opacity 0.2s ease;
}

.btn:hover {
  transform: scale(1.03);
  border-color: rgba(199, 210, 254, 0.5);
  box-shadow: 0 0 24px rgba(99, 102, 241, 0.42);
}

.btn:active {
  transform: scale(0.98);
}

.btn.ghost {
  background: rgba(30, 41, 59, 0.75);
  border: 1px solid rgba(148, 163, 184, 0.28);
  box-shadow: none;
}

.btn.ghost:hover {
  border-color: rgba(236, 72, 153, 0.4);
  box-shadow: 0 0 18px rgba(236, 72, 153, 0.2);
}
</style>
