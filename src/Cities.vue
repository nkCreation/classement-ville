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
        <td>
          <template v-if="index === 0">
            <img src="./assets/first.svg" alt="" />
          </template>
          <template v-else-if="index === 1">
            <img src="./assets/second.svg" alt="" />
          </template>
          <template v-else-if="index === 2">
            <img src="./assets/third.svg" alt="" />
          </template>
          <template v-else>
            {{ index + 1 }}
          </template>
        </td>
        <td>{{ city.name }}</td>
        <td data-title="Janzé" :style="getColor(city.janze)">
          {{ city.janze }}min
        </td>
        <td data-title="OF" :style="getColor(city.OF)">{{ city.OF }}min</td>
        <td data-title="ZNK" :style="getColor(city.ZNK)">{{ city.ZNK }}min</td>
      </tr>
    </tbody>
  </table>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import { City } from './models/City.model';

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
        val.ZNK ? acc.push(val.ZNK) : '';

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

img {
  width: 1.4em;
}

th,
td {
  text-align: left;

  &:nth-child(n + 3) {
    text-align: center;
  }
}

tr {
  border: 1px solid rgba(white, 0.16);
}

td {
  padding: 0.2em 0;
  background-color: rgba(var(--color), 0.32);

  &:first-child {
    text-align: center;
    font-weight: bold;
    font-size: 0.75em;
  }
}

@media (max-width: 640px) {
  tr,
  td,
  tbody,
  table {
    display: block;
    width: 100%;
  }

  thead {
    display: none;
  }

  tr {
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    grid-template-rows: auto auto;
    align-items: center;
    grid-template-areas:
      'position ville ville ville ville ville'
      'janze janze of of znk znk';
    margin: 1em 0;
    border: 1px solid rgba(white, 0.16);
    border-radius: 4px;
  }

  td {
    &:first-child {
      grid-area: position;
    }

    &:nth-child(2) {
      padding: 0.4em 0;
      grid-area: ville;
    }

    &:nth-child(3) {
      grid-area: janze;
    }
    &:nth-child(4) {
      grid-area: of;
    }
    &:nth-child(5) {
      grid-area: znk;
    }
  }
}
</style>
