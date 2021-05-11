<template>
  <!--<div>{{ repos }}</div>-->
  <input type="text" v-model="searchTag" placeholder="search query" />
  <input type="text" v-model="filter" placeholder="filter" />
  <table>
    <tbody>
      <tr v-for="repo in filteredRepos" :key="repo.package.name">
        <td>{{ repo.package.name }}</td>
        <td>{{ repo.package.version }}</td>
        <td>{{ repo.package.description }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>

const monsterMixin = {
  data() {
    return {
      npmsUri: "https://api.npms.io/v2/",
      repos: [],
      searchTag: this.tag,
      filter: "",
    };
  },
  methods: {
    async fetchRepos() {
      if (this.searchTag) {
        const response = await fetch(this.npmsUri + this.searchQuery);
        const responseData = await response.json();
        this.repos = responseData.results;
      }
    },
    updateFilters() {},
  },
  computed: {
    searchQuery() {
      return "search?q=" + this.searchTag;
    },
    filteredRepos() {
      return this.searchedRepos.filter((repo) => {
        return repo.package.name.toLowerCase().indexOf(this.filter) !== -1;
      });
    },
    searchedRepos() {
      return this.repos || [];
    },
  },
  watch: {
    tag: "fetchRepos",
    searchTag: "fetchRepos",
  },
  mounted() {
    this.fetchRepos();
  },
}

const myLogger = {

}

export default {
  name: "HelloWorld",
  mixins: [monsterMixin, myLogger],
  props: {
    tag: {
      type: String,
      required: true,
    },
  }
};
</script>

<style scoped lang="scss">
table {
  border-collapse: collapse;
  margin: 25px 0;
  font-size: 0.9em;
  font-family: sans-serif;
  min-width: 400px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
}
thead tr {
  background-color: #009879;
  color: #ffffff;
  text-align: left;
}
th,
td {
  padding: 12px 15px;
}
tbody tr {
  border-bottom: 1px solid #dddddd;
}
tbody tr:nth-of-type(even) {
  background-color: #f3f3f3;
}
tbody tr:last-of-type {
  border-bottom: 2px solid #009879;
}
tbody tr.active-row {
  font-weight: bold;
  color: #009879;
}
input {
  padding: 10px;
  border: 1px solid #ddd;
  margin: 10px;
  line-height: 32px;
  font-size: 28px;
}
</style>
