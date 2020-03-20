<template>
  <div class="container">
    <h1 class="text-center text-3xl">Corona Virus</h1>
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
  }
};
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS */
.container {
  @apply min-h-screen text-center mx-auto p-10;
}
</style>
