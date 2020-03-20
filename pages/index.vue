<template>
  <div class="container">
    <h1 class="text-center text-4xl">Coronavirus Statistics</h1>
    <Stats url="https://covid19.mathdro.id/api" />
    <CountriesState url="https://covid19.mathdro.id/api/countries" />
  </div>
</template>

<script>
import Logo from "~/components/Logo.vue";
import Stats from "~/components/Stats.vue";
import CountriesState from "~/components/CountriesState.vue";

export default {
  components: {
    Logo,
    Stats,
    CountriesState
  },
  data() {
    return {
      title: "Coronavirus statistics"
    };
  },
  head() {
    return {
      title: this.title,
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        {
          hid: this.title,
          name: this.title,
          content: this.title
        }
      ]
    };
  }
};
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS */
.container {
  @apply min-h-screen text-center mx-auto p-1 bg-gray-100;
}
@screen sm {
  .container {
    @apply p-10;
  }
}
</style>
