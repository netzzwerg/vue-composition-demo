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
import {
  ref,
  onMounted,
  watch,
  computed,
  toRefs,
  getCurrentInstance,
} from "vue";
export default {
  name: "HelloWorld",
  props: {
    tag: {
      type: String,
      required: true,
    },
  },
  setup(props) {
    const { tag } = toRefs(props); // helper to get all props reactive
    const npmsUri = "https://api.npms.io/v2/";
    const repos = ref([]);
    const searchTag = ref(tag.value);
    const filter = ref("");

    async function fetchRepos() {
      if (searchTag.value) {
        const response = await fetch(npmsUri + searchQuery.value);
        const responseData = await response.json();
        repos.value = responseData.results;
      }
    }

    // computed props
    const searchQuery = computed(() => {
      return "search?q=" + searchTag.value;
    });
    const filteredRepos = computed(() => {
      return searchedRepos.value.filter((repo) => {
        return repo.package.name.toLowerCase().indexOf(filter.value) !== -1;
      });
    });
    const searchedRepos = computed(() => {
      return repos.value || ref([]);
    });

    // lifecycle
    onMounted(() => {
      fetchRepos();
      // get current instance
      const internalInstance = getCurrentInstance();
      console.log(internalInstance); // component instance, dont use
      console.log(internalInstance.appContext.config); // appcontext good for global config settings
    });

    // watcher
    watch(tag, fetchRepos);
    watch(searchTag, fetchRepos);

    return {
      searchTag,
      filter,
      repos,
      fetchRepos,
      filteredRepos,
    };
  },
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
