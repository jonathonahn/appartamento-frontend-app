<template>
  <div class="groupsshow">
    <h1>{{ group.name }}</h1>
    <img :src="`${group.image}`" alt="group image" />
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
    <div v-for="user in group.users" v-bind:key="user">
      <p>
        <img class="user-image-group" :src="`${user.image}`" alt="user image" />
        {{ user.name }}
      </p>
      <p>{{ user.email }}</p>
    </div>
    <input type="text" v-model="newListingParams.address" />
    <button v-on:click="listingCreate()">Create Listing</button>
    <div v-for="listing in group.listings" v-bind:key="listing">
      <h3>
        <a :href="`${listing.url}`" target="_blank">{{ listing.address }}</a>
      </h3>
      <img
        class="listing-image"
        :src="`${listing.image}`"
        :alt="`${listing.address}`"
      />
      <br />
      <div v-for="comment in listing.comments" v-bind:key="comment">
        <span>{{ comment.user.name }}: {{ comment.text }} </span>
        <button
          v-on:click="commentDelete(comment.id)"
          v-if="comment.user.id === currentUser.id"
        >
          delete
        </button>
      </div>
      <input v-model="newCommentParams.text" />
      <button v-on:click="commentCreate(listing.id)">Add Comment</button>
      <button>More Info</button>
      <button v-on:click="listingDelete(listing.id)">Delete</button>
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
      errors: [],
      currentUser: {},
      newListingParams: {},
      editListingParams: {},
      newCommentParams: {},
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
    axios
      .get("/users/current")
      .then((response) => {
        console.log(response.data);
        this.currentUser = response.data;
      })
      .catch((error) => {
        this.errors = error.response.data.errors;
      });
  },
  methods: {
    listingCreate: function () {
      axios
        .post("/listings", this.newListingParams)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    listingUpdate: function (listingId) {
      axios
        .patch(`/listings/${listingId}`, this.editListingParams)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    listingDelete: function (listingId) {
      if (confirm("Delete listing?")) {
        axios
          .delete(`/listings/${listingId}`)
          .then((response) => {
            console.log(response.data);
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
    commentCreate: function (listingId) {
      this.newCommentParams.listing_id = listingId;
      this.newCommentParams.user_id = this.currentUser.id;
      axios
        .post("/comments", this.newCommentParams)
        .then((response) => {
          console.log(response.data);
          this.newCommentParams.text = "";
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    commentDelete: function (commentId) {
      if (confirm("Delete comment?")) {
        axios
          .delete(`/comments/${commentId}`)
          .then((response) => {
            console.log(response.data);
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
  },
};
</script>
