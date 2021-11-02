<template>
  <div class="groupsshow">
    <h1>{{ group.name }}</h1>
    <div v-for="user in group.users" v-bind:key="user.name">
      <p>{{ user }}</p>
    </div>
    <img :src="`${group.image}`" alt="group image" />
    <div v-for="listing in group.listings" v-bind:key="listing.name">
      <p>{{ listing }}</p>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      group: {},
    };
  },
  created: function () {
    axios
      .get("/groups/current")
      .then((response) => {
        console.log(response.data);
        this.group = response.data;
      })
      .catch((error) => {
        this.errors = error.response.data.errors;
      });
  },
  methods: {},
};
</script>
