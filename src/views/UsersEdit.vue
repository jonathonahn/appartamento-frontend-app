<template>
  <div class="users-edit">
    <div>
      <img :src="`${user.image}`" class="user-image" alt="user image" />
    </div>
    <h1>{{ user.name }}</h1>
    <form v-on:submit.prevent="submit()">
      <h3>Edit Profile</h3>
      <ul>
        <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
      <div>
        <label>Name:</label>
        &nbsp;
        <input type="text" v-model="editUserParams.name" />
      </div>
      <div>
        <label>Email:</label>
        &nbsp;
        <input type="email" v-model="editUserParams.email" />
      </div>
      <div>
        <label>Image:</label>
        &nbsp;
        <input type="text" v-model="editUserParams.image" />
      </div>
      <input class="btn btn-sm btn-secondary" type="submit" value="Submit" />
    </form>
    <br />
    <button class="btn btn-sm btn-danger" v-on:click="deleteUser()">
      Delete User
    </button>
  </div>
</template>

<style>
.user-image {
  max-height: 300px;
  width: auto;
  height: auto;
  border-radius: 50%;
}
.users-edit {
  text-align: center;
}
</style>

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
          this.$router.push("/groups/current");
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    deleteUser: function () {
      if (confirm("Are you sure you want to delete your account?")) {
        axios
          .delete(`/users/current`)
          .then((response) => {
            console.log(response.data);
            delete axios.defaults.headers.common["Authorization"];
            localStorage.removeItem("jwt");
            this.$router.push("/");
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
  },
};
</script>
