<template>
  <div class="groups-show">
    <h1>{{ group.name }}</h1>
    <img :src="`${group.image}`" alt="group image" />
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
    <div v-for="user in group.users" v-bind:key="user.id">
      <p>
        <img class="user-image-group" :src="`${user.image}`" alt="user image" />
        {{ user.name }}
      </p>
      <p>{{ user.email }}</p>
    </div>
    <input type="text" v-model="newListingParams.address" />
    <button v-on:click="listingCreate()">Create Listing</button>
    <div v-for="listing in group.listings" v-bind:key="listing.id">
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
        <span>{{ comment.user.name }}: {{ comment.text }} </span>
        <button
          v-on:click="commentDelete(comment, listing)"
          v-if="comment.user.id === currentUser.id"
        >
          delete
        </button>
      </div>
      <input v-model="newCommentParams.text" />
      <button v-on:click="commentCreate(listing)">Add Comment</button>
      <button>More Info</button>
      <button v-on:click="listingDelete(listing)">Delete</button>
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
      editGroupParams: {},
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
    groupUpdate: function () {
      axios
        .patch("/groups/current", this.editGroupParams)
        .then((response) => {
          console.log(response.data);
          this.group = response.data;
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    groupDelete: function () {
      if (confirm("Delete group?")) {
        axios
          .delete("/groups/current")
          .then((response) => {
            console.log(response.data);
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
    listingCreate: function () {
      axios
        .post("/listings", this.newListingParams)
        .then((response) => {
          console.log(response.data);
          this.group.listings.push(response.data);
          this.newListingParams.address = "";
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    listingUpdate: function (listing) {
      axios
        .patch(`/listings/${listing.id}`, this.editListingParams)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    listingDelete: function (listing) {
      if (confirm("Delete listing?")) {
        axios
          .delete(`/listings/${listing.id}`)
          .then((response) => {
            console.log(response.data); // splice stuff
            var index = this.group.listings.indexOf(listing);
            this.group.listings.splice(index, 1);
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
    commentCreate: function (listing) {
      this.newCommentParams.listing_id = listing.id;
      axios
        .post("/comments", this.newCommentParams)
        .then((response) => {
          console.log(response.data);
          this.newCommentParams.text = "";
          listing.comments.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    commentDelete: function (comment, listing) {
      if (confirm("Delete comment?")) {
        axios
          .delete(`/comments/${comment.id}`)
          .then((response) => {
            console.log(response.data); //splice comment out of listing
            listing;
            var index = this.listings.listing.comments.indexOf(comment);
            this.listings.listing.comments.splice(index, 1);
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
  },
};
</script>
