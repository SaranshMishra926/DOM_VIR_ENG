<template>
  <VirtualScroll
    ref="virtualList"
    class="list list--virtual"
    :items="items"
    :render-ahead="renderAhead"
    :estimated-height="estimatedHeight"
  >
    <template #item="{ item, index }">
      <PostCard :item="item" :index="index" :chip="chip" />
    </template>
  </VirtualScroll>
</template>

<script>
import { VirtualScroll } from '@/components';
import PostCard from './PostCard.vue';

export default {
  name: 'VirtualizedListPanel',
  components: {
    VirtualScroll,
    PostCard,
  },
  props: {
    items: {
      type: Array,
      default: () => [],
    },
    renderAhead: {
      type: Number,
      default: 5,
    },
    estimatedHeight: {
      type: Number,
      default: 44,
    },
    chip: {
      type: String,
      default: 'Live',
    },
  },
  methods: {
    getDomNodeCount() {
      if (!this.$refs.virtualList || !this.$refs.virtualList.$el) {
        return 0;
      }
      return this.$refs.virtualList.$el.querySelectorAll('*').length;
    },
    getRenderedCount() {
      if (!this.$refs.virtualList || !Array.isArray(this.$refs.virtualList.visibleItems)) {
        return 0;
      }
      return this.$refs.virtualList.visibleItems.length;
    },
    getScrollContainer() {
      if (!this.$refs.virtualList || !this.$refs.virtualList.$el) {
        return null;
      }
      return this.$refs.virtualList.$el;
    },
  },
};
</script>

<style scoped>
.list {
  position: relative;
  z-index: 1;
  border-radius: 16px;
  border: 1px solid rgba(148, 163, 184, 0.24);
  background: linear-gradient(160deg, rgba(15, 23, 42, 0.68), rgba(2, 6, 23, 0.48));
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.06);
}

.list--virtual {
  height: 540px;
  overflow: auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(129, 140, 248, 0.7) rgba(15, 23, 42, 0.45);
}

.list--virtual::-webkit-scrollbar {
  width: 8px;
}

.list--virtual::-webkit-scrollbar-thumb {
  border-radius: 999px;
  background: linear-gradient(180deg, rgba(129, 140, 248, 0.95), rgba(168, 85, 247, 0.88));
}

.list--virtual::-webkit-scrollbar-track {
  background: rgba(15, 23, 42, 0.45);
}
</style>
