<template>
  <div class="stat--container">
    <div class="stat-block">
      <h3>Confirmed:</h3>
      <span>{{ stats !== null && stats.confirmed ? stats.confirmed.value : 'Loading...'}}</span>
    </div>
    <div class="stat-block">
      <h3>Deaths:</h3>
      <span>{{ stats !== null && stats.confirmed ? stats.deaths.value : 'Loading...'}}</span>
    </div>
    <div class="stat-block">
      <h3>Recovered:</h3>
      <span>{{ stats !== null && stats.recovered ? stats.recovered.value : 'Loading...'}}</span>
    </div>
  </div>
</template>

<script>
export default {
  props: ["url"],
  data() {
    return {
      error: false,
      loading: true,
      stats: {}
    };
  },
  async mounted() {
    this.loading = true;
    this.fetchStats();
    this.loading = false;
  },
  methods: {
    async fetchStats() {
      try {
        const stats = await this.$axios.$get(this.url);
        this.stats = stats;
      } catch (error) {
        this.error = true;
      }
    }
  }
};
</script>

<style>
.stat--container {
  @apply grid grid-cols-3 gap-1 my-4;
}
.stat-block {
  @apply bg-gray-200 p-2;
}
.stat-block h3 {
  @apply text-4xl;
}
.stat-block span {
  @apply text-yellow-400 font-bold text-3xl;
}
</style>
