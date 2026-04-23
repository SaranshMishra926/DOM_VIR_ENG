<template>
  <div ref="normalContainer" class="list list--normal">
    <PostCard
      v-for="(item, index) in items"
      :key="`normal-${item.id}`"
      :item="item"
      :index="index"
      :chip="chip"
    />
  </div>
</template>

<script>
import PostCard from './PostCard.vue';

export default {
  name: 'NormalListPanel',
  components: {
    PostCard,
  },
  props: {
    items: {
      type: Array,
      default: () => [],
    },
    chip: {
      type: String,
      default: 'Live',
    },
  },
  methods: {
    getDomNodeCount() {
      if (!this.$refs.normalContainer) {
        return 0;
      }
      return this.$refs.normalContainer.querySelectorAll('*').length;
    },
    getRenderedCount() {
      return this.items.length;
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

.list--normal {
  height: 540px;
  overflow: auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(129, 140, 248, 0.7) rgba(15, 23, 42, 0.45);
}

.list--normal::-webkit-scrollbar {
  width: 8px;
}

.list--normal::-webkit-scrollbar-thumb {
  border-radius: 999px;
  background: linear-gradient(180deg, rgba(129, 140, 248, 0.95), rgba(168, 85, 247, 0.88));
}

.list--normal::-webkit-scrollbar-track {
  background: rgba(15, 23, 42, 0.45);
}
</style>
