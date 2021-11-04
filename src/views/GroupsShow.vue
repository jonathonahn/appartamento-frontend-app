<template>
  <div class="groups-show">
    <h1>{{ group.name }}</h1>
    <img :src="`${group.image}`" alt="group image" />
    <div>
      <button v-on:click="showEditGroup()">Edit</button>
    </div>
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
    <div>
      <div v-for="user in group.users" v-bind:key="user.id">
        <p>
          <img
            class="user-image-group"
            :src="`${user.image}`"
            alt="user image"
          />
          {{ user.name }}
        </p>
        <p>{{ user.email }}</p>
      </div>
    </div>
    <div>
      <button v-on:click="getReferralLink()">Referral Link</button>
    </div>
    <br />
    <div>
      <input type="text" v-model="newListingParams.address" />
      <button v-on:click="listingCreate()">Create Listing</button>
    </div>
    <div>
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
        <div>
          <span v-if="listing.bed">Beds: {{ listing.bed }}, </span>
          <span v-if="listing.bath">Baths: {{ listing.bath }}, </span>
          <span v-if="listing.squarefeet"
            >Square Feet: {{ listing.squarefeet }}
          </span>
        </div>
        <p>{{ listing.status }}</p>
        <div>
          <div v-for="comment in listing.comments" v-bind:key="comment.id">
            <span>{{ comment.user.name }}: {{ comment.text }} </span>
            <button
              v-on:click="commentDelete(comment, listing)"
              v-if="comment.user.id === currentUser.id"
            >
              delete
            </button>
          </div>
        </div>
        <input v-model="newCommentParams.text" />
        <button v-on:click="commentCreate(listing)">Add Comment</button>
        <div>
          <button v-on:click="showListing(listing)">Edit Listing</button>
        </div>

        <div>
          <button v-on:click="listingDelete(listing)">Delete Listing</button>
        </div>
      </div>
      <dialog id="group-edit">
        <form method="dialog">
          <p>Name: <input type="text" v-model="editGroupParams.name" /></p>
          <p>Image: <input type="text" v-model="editGroupParams.image" /></p>
          <button v-on:click="groupUpdate()">Update</button>
          <button>Close</button>
        </form>
      </dialog>
      <dialog id="listing-edit">
        <form method="dialog">
          <p>Beds: <input type="text" v-model="editListingParams.bed" /></p>
          <p>Baths: <input type="text" v-model="editListingParams.bath" /></p>
          <p>
            Area: <input type="text" v-model="editListingParams.squarefeet" />
          </p>
          <p>Image: <input type="text" v-model="editListingParams.image" /></p>
          <p>URL: <input type="text" v-model="editListingParams.url" /></p>
          <p>
            Status: <input type="text" v-model="editListingParams.status" />
          </p>
          <button v-on:click="listingUpdate(currentListing)">Update</button>
          <button>Close</button>
        </form>
      </dialog>
    </div>
    <div>
      <button v-on:click="groupDelete()">Delete Group?</button>
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
      currentListing: {},
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
    getReferralLink: function () {
      alert(`http://localhost:8080/signup?group_id=${this.group.id}`);
    },
    groupUpdate: function () {
      axios
        .patch("/groups/current", this.editGroupParams)
        .then((response) => {
          console.log(response.data);
          this.editGroupParams.name = "";
          this.editGroupParams.image = "";
          this.group = response.data;
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showEditGroup: function (group) {
      console.log(group);
      document.querySelector("#group-edit").showModal();
    },
    groupDelete: function () {
      if (confirm("Delete group?")) {
        if (confirm("This will delete all data and all accounts.")) {
          if (confirm("Are you sure?")) {
            axios
              .delete("/groups/current")
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
        }
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
      console.log(listing);
      axios
        .patch(`/listings/${listing.id}`, this.editListingParams)
        .then((response) => {
          console.log(response.data);
          this.editListingParams.status = "";
          this.editListingParams.url = "";
          this.editListingParams.image = "";
          this.editListingParams.bed = null;
          this.editListingParams.bath = null;
          this.editListingParams.squarefeet = null;
          var index = this.group.listings.indexOf(listing);
          this.group.listings[index] = response.data;
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showListing: function (listing) {
      console.log(listing);
      this.currentListing = listing;
      document.querySelector("#listing-edit").showModal();
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
            var index = listing.comments.indexOf(comment);
            listing.comments.splice(index, 1);
          })
          .catch((error) => {
            this.errors = error.response.data.errors;
          });
      }
    },
  },
};
</script>
