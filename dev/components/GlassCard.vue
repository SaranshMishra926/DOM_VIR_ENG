<template>
  <section
    class="glass-card"
    :class="[glow ? 'is-glow' : '', hoverable ? 'is-hoverable' : '']"
  >
    <slot />
  </section>
</template>

<script>
export default {
  name: 'GlassCard',
  props: {
    glow: {
      type: Boolean,
      default: false,
    },
    hoverable: {
      type: Boolean,
      default: true,
    },
  },
};
</script>

<style scoped>
.glass-card {
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.16);
  border-radius: 18px;
  background: linear-gradient(160deg, rgba(30, 41, 59, 0.46), rgba(15, 23, 42, 0.32));
  backdrop-filter: blur(18px);
  box-shadow: 0 10px 30px rgba(2, 6, 23, 0.4);
  transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease;
}

.glass-card::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  background: linear-gradient(145deg, rgba(99, 102, 241, 0.16), rgba(236, 72, 153, 0.08));
  pointer-events: none;
}

.glass-card::after {
  content: '';
  position: absolute;
  inset: 1px;
  border-radius: inherit;
  border: 1px solid rgba(255, 255, 255, 0.05);
  pointer-events: none;
}

.glass-card.is-hoverable:hover {
  transform: scale(1.03);
  border-color: rgba(129, 140, 248, 0.46);
  box-shadow: 0 16px 34px rgba(15, 23, 42, 0.48), 0 0 22px rgba(99, 102, 241, 0.26);
}

.glass-card.is-glow {
  box-shadow: 0 16px 34px rgba(15, 23, 42, 0.48), 0 0 24px rgba(99, 102, 241, 0.3);
}
</style>
