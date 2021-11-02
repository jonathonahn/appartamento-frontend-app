<template>
  <div class="usersedit">
    <p>{{ user }}</p>
    <form v-on:submit.prevent="submit()">
      <h1>Edit User</h1>
      <ul>
        <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
      <div>
        <label>Name: </label>
        <input type="text" v-model="editUserParams.name" />
      </div>
      <div>
        <label>Email: </label>
        <input type="email" v-model="editUserParams.email" />
      </div>
      <div>
        <label>Image: </label>
        <input type="text" v-model="editUserParams.image" />
      </div>
      <input type="submit" value="Submit" />
    </form>
    <button v-on:click="deleteUser()">Delete User</button>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      user: {},
      errors: [],
      editUserParams: {},
      showModal: false,
    };
  },
  created: function () {
    axios
      .get("/users/current")
      .then((response) => {
        console.log(response.data);
        this.user = response.data;
      })
      .catch((error) => {
        this.errors = error.response.data.errors;
      });
  },
  methods: {
    submit: function () {
      axios
        .patch("/users/current", this.editUserParams)
        .then((response) => {
          console.log(response.data);
          this.$router.push("/groupsshow");
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    deleteUser: function () {
      console.log("haha delete user");
    },
  },
};
</script>
