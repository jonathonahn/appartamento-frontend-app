<template>
  <div class="groupsshow">
    <h1>{{ group.name }}</h1>
    <img :src="`${group.image}`" alt="group image" />
    <div v-for="user in group.users" v-bind:key="user.name">
      <p>{{ user.name }}</p>
      <p>{{ user.email }}</p>
      <img :src="`${user.image}`" alt="user image" />
    </div>

    <div v-for="listing in group.listings" v-bind:key="listing.name">
      <a :href="`${listing.url}`" target="_blank">{{ listing.address }}</a>
      <br />
      <img :src="`${listing.image}`" :alt="`${listing.address}`" />
      <div v-for="comment in listing.comments" v-bind:key="comment.id">
        <p>{{ comment.user.name }}: {{ comment.text }}</p>
      </div>
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
