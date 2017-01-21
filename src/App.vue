<template>
  <div id="app">
    <input class="column-names-input" type="text" v-model="columnNamesInput"></input>
    <table cellspacing="0">
      <thead>
        <tr>
          <th v-for="name in allColumnNames"><input readonly :value="name"></input></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(r, index) in rows">
          <td v-for="name in allColumnNames">
            <input :value="r[name]"
                   v-focus="index === rowIndexFocussed && name === columnNameFocussed"
                   :class="{ hightlight1 : index === rowIndexFocussed }"
                   @focus="focus(index, name)"
                   @input="$set(r, name, arguments[0].target.value)"
                   @keydown.alt.88="deleteRow(index)"
                   @keydown.down="down(index, name, $event)"
                   @keydown.up="up(index, name, $event)"
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
import LZString from 'lz-string';

Vue.directive('focus', (el, binding) => {
  if (binding.value && !binding.oldValue) {
    Vue.nextTick(() => {
      el.focus();
    });
  }
});

export default {
  name: 'app',
  components: {
  },
  mounted() {
    if (window.location.hash) {
      const hashString = window.location.hash.slice(1);
      const jsonString = LZString.decompressFromEncodedURIComponent(hashString);
      const data = JSON.parse(jsonString);
      this.columnNames = data.columnNames;
      this.rows = data.rows;
    }
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
    up(index, name, event) {
      if (event.shiftKey) {
        this.insertRow(index, name);
      } else {
        this.focus(index - 1, name);
      }
    },
    down(index, name, event) {
      if (event.shiftKey) {
        this.insertRow(index + 1, name);
      } else {
        this.focus(index + 1, name);
      }
    },
    updateHash() {
      window.location.hash = LZString.compressToEncodedURIComponent(JSON.stringify({
        columnNames: this.columnNames,
        rows: this.rows,
      }));
    },
    focus(index, columnName) {
      const index2 = (index + this.rows.length) % this.rows.length;
      this.rowIndexFocussed = index2;
      this.columnNameFocussed = columnName;
    },
    insertRow(index, name) {
      this.rows = [...this.rows.slice(0, index), {}, ...this.rows.slice(index)];
      Vue.nextTick(() => {
        Vue.nextTick(() => {
          this.focus(index, name);
        });
      });
    },
    deleteRow(index) {
      this.rows.splice(index, 1);
    },
  },
  watch: {
    columnNames() {
      this.updateHash();
    },
    rows: {
      handler() {
        this.updateHash();
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

th {
  padding: 0px;
}

th > input {
  text-align: center;
}

td {
  padding: 0px;
}

input {
  border: 1px solid #cccccc; /* Here */
  -webkit-appearance: none; 
  -moz-appearance: none; 
}

td > input:focus {
  background-color: #cccccc;
}

.column-names-input {
  width : 100%;
}

.hightlight1 {
  background-color: #eeeeee;
}

</style>
