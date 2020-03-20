<template>
  <div class>
    <table class="table-auto w-full">
      <thead>
        <tr>
          <th class="p-2 border">Country</th>
          <th class="p-2 border">Cases</th>
          <th class="p-2 border">Deaths</th>
          <th class="p-2 border">Recovered</th>
          <th class="p-2 border">Last updated</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(country,index) in countries" v-bind:key="index">
          <td class="p-2 border">{{country.country}}</td>
          <td class="p-2 border">{{ country.stats ? country.stats.confirmed.value : '0'}}</td>
          <td class="p-2 border">{{ country.stats ? country.stats.deaths.value : '0'}}</td>
          <td class="p-2 border">{{ country.stats ? country.stats.recovered.value : '0' }}</td>
          <td
            class="p-2 border"
          >{{ country.stats ? new Date(country.stats.lastUpdate).toUTCString() : '0' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: ["url"],
  data() {
    return {
      error: [],
      loading: true,
      countries: []
    };
  },
  mounted() {
    this.fetchCountries();
  },
  methods: {
    async fetchCountries() {
      const countries = await this.$axios
        .$get(this.url)
        .catch(err => console.log(err));

      delete countries.countries["Mainland China"];
      const c = Object.entries(countries.countries).map(
        async ([country, code]) => {
          const codeCountry = countries.iso3[code];
          const keys = Object.keys(countries.iso3);
          if (keys.includes(countries.countries[country])) {
            const stats = await this.$axios
              .$get(`https://covid19.mathdro.id/api/countries/${codeCountry}`)
              .catch(error => {});
            /*const confirmedInCountry = await this.$axios
              .$get(
                `https://covid19.mathdro.id/api/countries/${codeCountry}/confirmed`
              )
              .catch(error => {});*/
            return {
              country,
              codeCountry,
              stats
            };
          }
        }
      );

      const data = await Promise.all(c);
      const sort = data
        .filter(elem => elem.stats != undefined)
        .sort(function(a, b) {
          if (a.stats != undefined && b.stats != undefined) {
            return b.stats.confirmed.value - a.stats.confirmed.value;
          }
        });
      this.countries = sort;
    }
  }
};
</script>

<style>
.stat--container {
  @apply my-4;
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
