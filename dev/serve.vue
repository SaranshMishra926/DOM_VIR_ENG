<script>
import Vue from 'vue';
import GlassCard from './components/GlassCard.vue';
import HeaderBar from './components/HeaderBar.vue';
import Controls from './components/Controls.vue';
import NormalListPanel from './components/NormalListPanel.vue';
import VirtualizedListPanel from './components/VirtualizedListPanel.vue';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    GlassCard,
    HeaderBar,
    Controls,
    NormalListPanel,
    VirtualizedListPanel,
  },
  data() {
    return {
      items: Array.from({ length: 5000 }, (_, index) => ({
        id: index + 1,
        label: `Row ${index + 1}`,
      })),
      renderAhead: 5,
      estimatedHeight: 44,
      speedMode: 'normal',
      fps: 60,
      frameDrops: 0,
      rafId: null,
      metricsRafId: null,
      virtualMetricsRafId: null,
      fpsLoopStart: 0,
      fpsFrameCounter: 0,
      normalRenderMs: 0,
      virtualRenderMs: 0,
      scrollUpdateMs: 0,
      mountStartTime: 0,
      fullDomNodes: 0,
      normalDomNodes: 0,
      virtualDomNodes: 0,
      virtualScrollEl: null,
    };
  },
  computed: {
    speedLabel() {
      return this.speedMode === 'normal' ? 'Normal Stream' : 'High Activity Stream';
    },
    streamTag() {
      return this.speedMode === 'normal' ? 'Live' : 'Volatile';
    },
    domNodesSaved() {
      return Math.max(0, this.normalDomNodes - this.virtualDomNodes);
    },
    timeSavedMs() {
      return Math.max(0, this.normalRenderMs - this.virtualRenderMs);
    },
  },
  mounted() {
    this.mountStartTime = performance.now();
    this.measurePanelMounts();
    this.initVirtualScrollTracking();
    this.startLiveMetricsLoop();
    this.refreshDomStats();
    this.startFpsLoop();
  },
  beforeDestroy() {
    if (this.rafId) {
      window.cancelAnimationFrame(this.rafId);
    }
    if (this.metricsRafId) {
      window.cancelAnimationFrame(this.metricsRafId);
    }
    if (this.virtualMetricsRafId) {
      window.cancelAnimationFrame(this.virtualMetricsRafId);
    }
    if (this.virtualScrollEl) {
      this.virtualScrollEl.removeEventListener('scroll', this.onVirtualScroll);
      this.virtualScrollEl = null;
    }
  },
  methods: {
    measurePanelMounts() {
      const normalStart = performance.now();
      this.$nextTick(() => {
        const normalEnd = performance.now();
        this.normalRenderMs = Math.max(0.01, normalEnd - normalStart);

        const virtualStart = performance.now();
        window.requestAnimationFrame(() => {
          const virtualEnd = performance.now();
          this.virtualRenderMs = Math.max(0.01, virtualEnd - virtualStart);
        });
      });
    },
    startFpsLoop() {
      const loop = (time) => {
        this.fpsFrameCounter += 1;
        if (this.fpsLoopStart === 0) {
          this.fpsLoopStart = time;
        }
        if (time - this.fpsLoopStart >= 1000) {
          this.fps = this.fpsFrameCounter;
          if (this.fps < 50) {
            this.frameDrops += 1;
          }
          this.fpsFrameCounter = 0;
          this.fpsLoopStart = time;
        }
        this.rafId = window.requestAnimationFrame(loop);
      };
      this.rafId = window.requestAnimationFrame(loop);
    },
    startLiveMetricsLoop() {
      const update = () => {
        this.refreshDomStats();
        this.metricsRafId = window.requestAnimationFrame(update);
      };
      this.metricsRafId = window.requestAnimationFrame(update);
    },
    initVirtualScrollTracking() {
      this.$nextTick(() => {
        if (!this.$refs.virtualPanel) {
          return;
        }
        this.virtualScrollEl = this.$refs.virtualPanel.getScrollContainer();
        if (!this.virtualScrollEl) {
          return;
        }
        this.virtualScrollEl.addEventListener('scroll', this.onVirtualScroll, { passive: true });
      });
    },
    onVirtualScroll() {
      const scrollStart = performance.now();
      if (this.virtualMetricsRafId) {
        window.cancelAnimationFrame(this.virtualMetricsRafId);
      }
      this.virtualMetricsRafId = window.requestAnimationFrame(() => {
        const scrollEnd = performance.now();
        this.scrollUpdateMs = Math.max(0.01, scrollEnd - scrollStart);

        const virtualStart = performance.now();
        this.virtualMetricsRafId = window.requestAnimationFrame(() => {
          const virtualEnd = performance.now();
          this.virtualRenderMs = Math.max(0.01, virtualEnd - virtualStart);
          this.refreshDomStats();
        });
      });
    },
    refreshDomStats() {
      this.fullDomNodes = document.querySelectorAll('*').length;
      this.normalDomNodes = this.getNormalDomNodes();
      this.virtualDomNodes = this.getVirtualDomNodes();
    },
    increaseRenderAhead() {
      this.renderAhead = Math.min(30, this.renderAhead + 1);
    },
    decreaseRenderAhead() {
      this.renderAhead = Math.max(1, this.renderAhead - 1);
    },
    toggleSpeed() {
      this.speedMode = this.speedMode === 'normal' ? 'high' : 'normal';
    },
    getVirtualRenderedCount() {
      if (!this.$refs.virtualPanel) {
        return 0;
      }
      return this.$refs.virtualPanel.getRenderedCount();
    },
    getNormalDomNodes() {
      if (!this.$refs.normalPanel) {
        return 0;
      }
      return this.$refs.normalPanel.getDomNodeCount();
    },
    getVirtualDomNodes() {
      if (!this.$refs.virtualPanel) {
        return 0;
      }
      return this.$refs.virtualPanel.getDomNodeCount();
    },
    getFpsTone() {
      if (this.fps >= 50) {
        return 'good';
      }
      if (this.fps < 30) {
        return 'bad';
      }
      return 'neutral';
    },
    getFrameDropsTone() {
      return this.frameDrops > 0 ? 'bad' : 'good';
    },
    formatMs(value) {
      return `${Number(value || 0).toFixed(2)}ms`;
    },
  },
});
</script>

<template>
  <div id="app" class="dashboard">
    <div class="dashboard__overlay" />
    <div class="dashboard__content">
      <HeaderBar />
      <Controls
        :speed-label="speedLabel"
        @smaller-ahead="decreaseRenderAhead"
        @larger-ahead="increaseRenderAhead"
        @toggle-speed="toggleSpeed"
      />

      <main class="dashboard__grid">
        <GlassCard class="panel panel--normal">
          <div class="panel-header">
            <h2>Normal Rendering</h2>
            <p>Rendering every row in the DOM.</p>
          </div>
          <NormalListPanel ref="normalPanel" :items="items" :chip="streamTag" />
        </GlassCard>

        <GlassCard class="panel panel--virtual" :glow="true">
          <div class="panel-header">
            <h2>Virtualized Rendering</h2>
            <p>Main focus: only visible rows are mounted.</p>
          </div>
          <VirtualizedListPanel
            ref="virtualPanel"
            :items="items"
            :render-ahead="renderAhead"
            :estimated-height="estimatedHeight"
            :chip="streamTag"
          />
        </GlassCard>

        <GlassCard class="panel panel--metrics">
          <div class="panel-header panel-header--metrics">
            <h2>Performance Metrics</h2>
            <p>Live dashboard for rendering and DOM behavior.</p>
          </div>

          <div class="metrics-sections">
            <section class="metrics-section">
              <h3>Rendering Stats</h3>
              <div class="metrics-grid">
                <article class="metric-card">
                  <p class="metric-card__label">Total Items</p>
                  <p class="metric-card__value metric-card__value--neutral">{{ items.length.toLocaleString() }}</p>
                </article>
                <article class="metric-card">
                  <p class="metric-card__label">Rendered (Normal)</p>
                  <p class="metric-card__value metric-card__value--neutral">{{ items.length.toLocaleString() }}</p>
                </article>
                <article class="metric-card">
                  <p class="metric-card__label">Rendered (Virtual)</p>
                  <p class="metric-card__value metric-card__value--neutral">{{ getVirtualRenderedCount().toLocaleString() }}</p>
                </article>
              </div>
            </section>

            <section class="metrics-section">
              <h3>DOM Stats</h3>
              <div class="metrics-grid">
                <article class="metric-card">
                  <p class="metric-card__label">DOM Nodes (Normal)</p>
                  <p class="metric-card__value metric-card__value--neutral">{{ normalDomNodes.toLocaleString() }}</p>
                </article>
                <article class="metric-card">
                  <p class="metric-card__label">DOM Nodes (Virtual)</p>
                  <p class="metric-card__value metric-card__value--neutral">{{ virtualDomNodes.toLocaleString() }}</p>
                </article>
              </div>
            </section>

            <section class="metrics-section">
              <h3>Performance Stats</h3>
              <div class="metrics-grid">
                <article class="metric-card">
                  <p class="metric-card__label">FPS</p>
                  <p class="metric-card__value" :class="`metric-card__value--${getFpsTone()}`">{{ fps.toFixed(1) }}</p>
                </article>
                <article class="metric-card">
                  <p class="metric-card__label">Frame Drops</p>
                  <p class="metric-card__value" :class="`metric-card__value--${getFrameDropsTone()}`">{{ frameDrops }}</p>
                </article>
                <article class="metric-card">
                  <p class="metric-card__label">Scroll Update Time</p>
                  <p class="metric-card__value metric-card__value--neutral">{{ formatMs(scrollUpdateMs) }}</p>
                  <p class="metric-card__hint">Virtual scroll handler execution time</p>
                </article>
                <article class="metric-card">
                  <p class="metric-card__label">Initial Mount</p>
                  <p class="metric-card__value metric-card__value--neutral">{{ formatMs(normalRenderMs) }}</p>
                </article>
              </div>
            </section>

            <section class="metrics-section">
              <h3>Improvements</h3>
              <div class="metrics-grid">
                <article class="metric-card">
                  <p class="metric-card__label">DOM Nodes Saved</p>
                  <p class="metric-card__value metric-card__value--good">{{ domNodesSaved.toLocaleString() }}</p>
                </article>
                <article class="metric-card">
                  <p class="metric-card__label">Time Saved</p>
                  <p class="metric-card__value metric-card__value--good">{{ formatMs(timeSavedMs) }}</p>
                </article>
              </div>
            </section>
          </div>
        </GlassCard>
      </main>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.dashboard {
  position: relative;
  min-height: 100vh;
  padding: 24px;
  font-family: Inter, 'Segoe UI', Roboto, Arial, sans-serif;
  background: linear-gradient(135deg, #020617 0%, #0f172a 46%, #1e293b 100%);
  color: #ffffff;
}

.dashboard__overlay {
  position: absolute;
  inset: 0;
  pointer-events: none;
  background:
    radial-gradient(circle at 16% 0%, rgba(56, 189, 248, 0.13), transparent 36%),
    radial-gradient(circle at 84% 10%, rgba(236, 72, 153, 0.2), transparent 38%),
    radial-gradient(circle at 52% 100%, rgba(99, 102, 241, 0.24), transparent 42%);
}

.dashboard__content {
  position: relative;
  z-index: 1;
  max-width: 1500px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 18px;
}

.dashboard__grid {
  display: grid;
  grid-template-columns: 1fr 1.25fr 0.88fr;
  gap: 18px;
  align-items: start;
  min-width: 1120px;
}

.panel {
  display: flex;
  flex-direction: column;
  padding: 18px;
}

.panel-header {
  position: relative;
  z-index: 1;
  margin-bottom: 14px;
}

.panel-header h2 {
  margin: 0;
  font-size: 16px;
  font-weight: 700;
  letter-spacing: 0.015em;
  color: #f8fafc;
}

.panel-header p {
  margin: 6px 0 0;
  color: #94a3b8;
  font-size: 12px;
  line-height: 1.5;
}

.metrics-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 10px;
}

.panel--metrics {
  background: #f8fafc;
}

.panel-header--metrics h2 {
  color: #111827;
}

.panel-header--metrics p {
  color: #6b7280;
}

.metrics-sections {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.metrics-section h3 {
  margin: 0 0 8px;
  color: #111827;
  font-size: 13px;
  font-weight: 700;
}

.metric-card {
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  background: #ffffff;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
  padding: 10px 12px;
  min-height: 74px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 4px;
}

.metric-card__label {
  margin: 0;
  color: #6b7280;
  font-size: 11px;
  font-weight: 600;
  line-height: 1.3;
}

.metric-card__value {
  margin: 0;
  font-size: 21px;
  font-weight: 700;
  line-height: 1.2;
}

.metric-card__hint {
  margin: 0;
  color: #9ca3af;
  font-size: 10px;
  line-height: 1.3;
}

.metric-card__value--good {
  color: #16a34a;
}

.metric-card__value--bad {
  color: #dc2626;
}

.metric-card__value--neutral {
  color: #4b5563;
}

@media (max-width: 680px) {
  .metrics-grid {
    grid-template-columns: 1fr;
  }
}

</style>
