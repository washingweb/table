<template>
  <div id="app">
    <input class="column-names-input" type="text" v-model="columnNamesInput"></input>
    <MyTable :rows="rows" :column-names="columnNames" />
  </div>
</template>

<script>

import MyTable from './components/Table';

export default {
  name: 'app',
  components: {
    MyTable,
  },
  data() {
    return {
      rows: [],
      columnNames: ['a', 'b', 'c', 'd'],
    };
  },
  computed: {
    columnNamesInput: {
      get() {
        return this.columnNames.join(',');
      },
      set(newValue) {
        this.columnNames = newValue
                              .trim()
                              .split(/,|，/)
                              .map(k => k.trim());
      },
    },
  },
};
</script>

<style>

* {
  box-sizing: border-box;
}

html, body {
  margin  : 0px;
  padding : 0px;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
</style>

<style scoped>
.column-names-input {
  width : 100%;
}
</style>
