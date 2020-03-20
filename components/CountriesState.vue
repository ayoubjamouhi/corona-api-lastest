<template>
  <table
    class="w-full flex flex-row flex-no-wrap sm:bg-white rounded-lg overflow-hidden sm:shadow-lg my-5"
  >
    <thead class="text-white">
      <tr
        class="bg-teal-400 flex flex-col flex-no wrap sm:table-row rounded-l-lg sm:rounded-none mb-2 sm:mb-0"
        v-for="(country,index) in countries"
        v-bind:key="index"
      >
        <th class="p-3 text-left">Country</th>
        <th class="p-3 text-left">Cases</th>
        <th class="p-3 text-left">Deaths</th>
        <th class="p-3 text-left">Recovered</th>
        <th class="p-3 text-left">Last updated</th>
      </tr>
    </thead>
    <tbody class="flex-1 sm:flex-none">
      <tr
        class="flex flex-col flex-no wrap sm:table-row mb-2 sm:mb-0"
        v-for="(country,index) in countries"
        v-bind:key="index"
      >
        <td class="border-grey-light border hover:bg-gray-100 p-3">{{country.country}}</td>
        <td
          class="border-grey-light border hover:bg-gray-100 p-3"
        >{{ country.stats ? country.stats.confirmed.value : '0'}}</td>
        <td
          class="border-grey-light border hover:bg-gray-100 p-3"
        >{{ country.stats ? country.stats.deaths.value : '0'}}</td>
        <td
          class="border-grey-light border hover:bg-gray-100 p-3"
        >{{ country.stats ? country.stats.recovered.value : '0' }}</td>
        <td
          class="border-grey-light border hover:bg-gray-100 p-3"
        >{{ country.stats ? new Date(country.stats.lastUpdate).toLocaleString() : '0' }}</td>
      </tr>
    </tbody>
  </table>
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
@media (min-width: 640px) {
  table {
    display: inline-table !important;
  }

  thead tr:not(:first-child) {
    display: none;
  }
}

td:not(:last-child) {
  border-bottom: 0;
}

th:not(:last-child) {
  border-bottom: 2px solid rgba(0, 0, 0, 0.1);
}
</style>
