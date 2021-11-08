<template>
  <div class="signup">
    <form v-on:submit.prevent="submit()">
      <h1>Signup</h1>
      <ul>
        <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
      <div v-if="!newUserParams.group_id">
        <label>Group Name: </label>
        &nbsp;
        <input type="text" v-model="newUserParams.group_name" />
      </div>
      <div>
        <label>Name: </label>
        &nbsp;
        <input type="text" v-model="newUserParams.name" />
      </div>
      <div>
        <label>Email: </label>
        &nbsp;
        <input type="email" v-model="newUserParams.email" />
      </div>
      <div>
        <label>Password: </label>
        &nbsp;
        <input type="password" v-model="newUserParams.password" />
      </div>
      <div>
        <label>Password confirmation: </label>
        &nbsp;
        <input type="password" v-model="newUserParams.password_confirmation" />
      </div>
      <input class="btn btn-secondary btn-sm" type="submit" value="Submit" />
    </form>
  </div>
</template>
<style>
.signup {
  text-align: center;
}
</style>
<script>
import axios from "axios";
export default {
  data: function () {
    return {
      newUserParams: {},
      errors: [],
    };
  },
  created: function () {
    this.newUserParams.group_id = this.$route.query.group_id;
  },
  methods: {
    submit: function () {
      axios
        .post("/users", this.newUserParams)
        .then((response) => {
          console.log(response.data);
          this.$router.push("/login");
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
