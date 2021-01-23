<template>
  <table>
    <thead>
      <tr>
        <th></th>
        <th>Ville</th>
        <th>Janzé</th>
        <th>OF</th>
        <th>ZNK</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(city, index) in cities" :key="city.name">
        <td>{{ index + 1 }}</td>
        <td>{{ city.name }}</td>
        <td :style="getColor(city.janze)">{{ city.janze }}min</td>
        <td :style="getColor(city.OF)">{{ city.OF }}min</td>
        <td :style="getColor(city.ZNK)">{{ city.ZNK }}min</td>
      </tr>
    </tbody>
  </table>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import { City } from './models/City.model';

/* tslint:disable-next-line */
import chroma from 'chroma-js';

const scale = chroma.scale(['green', 'red']);

export default defineComponent({
  name: 'Cities',
  props: {
    cities: {
      type: Array as PropType<City[]>,
      default: () => [],
    },
  },
  methods: {
    getColor(timeScale: number): string {
      return `--color: ${chroma(this.chromaScale(timeScale)).rgb().join(',')}`;
    },
  },
  computed: {
    domain(): string {
      const bornes = this.cities.reduce((acc, val) => {
        acc.push(val.janze);
        acc.push(val.OF);
        acc.push(val.ZNK);

        return acc;
      }, [] as number[]);

      return `${Math.min(...bornes)}-${Math.max(...bornes)}`;
    },
    chromaScale(): any {
      const split = this.domain.split('-');
      return scale.domain(split);
    },
  },
});
</script>

<style lang="scss" scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  text-align: left;

  &:nth-child(n + 3) {
    text-align: center;
  }
}

td {
  background-color: rgba(var(--color), 0.32);
}
</style>
