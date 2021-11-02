<template>
  <div class="groupsshow">
    <h1>{{ group.name }}</h1>
    <img :src="`${group.image}`" alt="group image" />
    <div v-for="user in group.users" v-bind:key="user.name">
      <p>
        <img class="user-image-group" :src="`${user.image}`" alt="user image" />
        {{ user.name }}
      </p>
      <p>{{ user.email }}</p>
    </div>

    <div v-for="listing in group.listings" v-bind:key="listing.name">
      <h3>
        <a :href="`${listing.url}`" target="_blank">{{ listing.address }}</a>
      </h3>
      <img
        class="listing-image"
        :src="`${listing.image}`"
        :alt="`${listing.address}`"
      />
      <br />
      <div v-for="comment in listing.comments" v-bind:key="comment.id">
        <p>{{ comment.user.name }}: {{ comment.text }}</p>
      </div>
      <button>More Info</button>
    </div>
  </div>
</template>

<style>
.user-image-group {
  height: 50px;
  border-radius: 50%;
}
.listing-image {
  height: 300px;
}
a {
  text-decoration: none;
  color: black;
}
a:visited {
  text-decoration: none;
  color: black;
}
</style>

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
