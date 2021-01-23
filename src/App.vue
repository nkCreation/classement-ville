<template>
  <div>
    <h1>Classement des villes</h1>
    <p>En fonction des distances entre l'école et le travail.</p>

    <form>
      <legend>Méthode de tri :</legend>

      <label v-for="sort in sorting" :key="sort.name">
        <input
          type="radio"
          name="sortingAlgorithm"
          :value="sort.value"
          v-model="sortingAlgorithm"
        />
        {{ sort.name }}
      </label>
    </form>

    <h2>Résultats :</h2>
    <Cities :cities="sortedCities" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Cities from './Cities.vue';
import { City } from './models/City.model';
import { cities } from './assets/data';

export default defineComponent({
  name: 'App',
  components: {
    Cities,
  },
  data: () => ({
    sortingAlgorithm: 'classic',
    cities,
    sorting: [
      { name: 'Équidistance', value: 'classic' },
      { name: 'OF', value: 'OF' },
      { name: 'Janzé', value: 'janze' },
    ],
  }),
  computed: {
    sortedCities(): City[] {
      switch (this.sortingAlgorithm) {
        case 'janze':
          return this.sortedByKey('janze');

        case 'OF':
          return this.sortedByKey('OF');

        default:
          return this.sortedCitiesByDiff;
      }
    },
    sortedCitiesByDiff(): City[] {
      return [...this.cities].sort((a, b) => {
        const diffA = a.OF - a.janze;
        const diffB = b.OF - b.janze;
        return Math.abs(diffA) - Math.abs(diffB);
      });
    },
  },
  methods: {
    sortedByKey(key: keyof City): City[] {
      return [...this.cities].sort((a, b) => {
        /* tslint:disable-next-line */
        return parseInt(a[key], 10) - parseInt(b[key], 10);
      });
    },
  },
});
</script>

<style lang="scss" scoped>
div {
  max-width: 600px;
  margin: 0 auto;
}
label {
  display: block;
}
</style>
