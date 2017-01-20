<template>
  <table>
    <thead>
      <tr>
        <th v-for="name in columnNames">{{ name }}</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>
</template>

<script>
export default {
  props: {
    rows: {
      default() {
        return [];
      },
    },
    columnNames: {
      default() {
        return ['a', 'b', 'c'];
      },
    },
  },
};
</script>

<style>
</style>
