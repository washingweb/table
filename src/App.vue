<template>
  <div id="app">
    <input class="column-names-input" type="text" v-model="columnNamesInput"></input>
    <table cellspacing="0">
      <thead>
        <tr>
          <th v-for="name in allColumnNames">{{ name }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(r, index) in rows">
          <td v-for="name in allColumnNames">
            <input :value="r[name]"
                   v-focus="index === rowIndexFocussed && name === columnNameFocussed"
                   @input="$set(r, name, arguments[0].target.value)"
                   @keydown.enter="insertRow(index+1); focus(index+1, name);"
                   @keydown.shift.enter="insertRow(index); focus(index, name);"
                   @keydown.ctrl.88="deleteRow(index)"
                   @keydown.down="focus(index+1, name)"
                   @keydown.up="focus(index-1, name)"
                   />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

import Vue from 'vue';
import _ from 'lodash';

Vue.directive('focus', {
  update(el, binding) {
    if (binding.value) {
      Vue.nextTick(() => {
        el.focus();
      });
    }
  },
});

export default {
  name: 'app',
  components: {
  },
  data() {
    return {
      rows: [
        {},
      ],
      columnNames: [],
      rowIndexFocussed: undefined,
      columnNameFocussed: undefined,
    };
  },
  computed: {
    allColumnNames() {
      const rowsNames = _.uniq(_.flatten(this.rows.map(r => Object.keys(r))));
      return _.uniq([...this.columnNames, ...rowsNames]);
    },
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
  methods: {
    focus(index, columnName) {
      const index2 = (index + this.rows.length) % this.rows.length;
      this.rowIndexFocussed = index2;
      this.columnNameFocussed = columnName;
    },
    insertRow(index) {
      this.rows = [...this.rows.slice(0, index), {}, ...this.rows.slice(index)];
    },
    deleteRow(index) {
      this.rows.splice(index, 1);
    },
  },
  watch: {
    rows: {
      handler() {
      },
      deep: true,
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

table {
  border-spacing: 0;
  border-collapse: collapse;
}

td {
  padding: 0px;
}

.column-names-input {
  width : 100%;
}
</style>
