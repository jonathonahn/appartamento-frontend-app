<template>
  <div class="signup">
    <form v-on:submit.prevent="submit()">
      <h1>Signup</h1>
      <ul>
        <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
      <div v-if="!newUserParams.group_id">
        <label>Group Name: </label>
        <input type="text" v-model="newGroupParams.name" />
      </div>
      <div>
        <label>Name: </label>
        <input type="text" v-model="newUserParams.name" />
      </div>
      <div>
        <label>Email: </label>
        <input type="email" v-model="newUserParams.email" />
      </div>
      <div>
        <label>Password: </label>
        <input type="password" v-model="newUserParams.password" />
      </div>
      <div>
        <label>Password confirmation: </label>
        <input type="password" v-model="newUserParams.password_confirmation" />
      </div>
      <input type="submit" value="Submit" />
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      newUserParams: {},
      newGroupParams: {},
      errors: [],
    };
  },
  created: function () {
    this.newUserParams["group_id"] = this.$route.query.group_id;
  },
  methods: {
    submit: function () {
      if (this.newGroupParams.name) {
        axios
          .post("/groups/current", this.newGroupParams)
          .then((response) => {
            console.log(response.data);
            this.newUserParams["group_id"] = response.data.id;
            axios
              .post("/users", this.newUserParams)
              .then((response) => {
                console.log(response.data);
                this.$router.push("/login");
              })
              .catch((error) => {
                this.errors = error.response.data.errors;
              });
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      } else {
        axios
          .post("/users", this.newUserParams)
          .then((response) => {
            console.log(response.data);
            this.$router.push("/login");
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
  },
};
</script>
