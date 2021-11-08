<template>
  <div class="groups-show">
    <datalist id="statuses">
      <option v-for="status in statuses" v-bind:key="status">
        {{ status }}
      </option>
    </datalist>
    <h1 v-if="group.name">{{ group.name }}</h1>
    <img :src="`${group.image}`" alt="group image" />
    <div>
      <button class="btn btn-primary" v-on:click="showEditGroup()">Edit</button>
    </div>
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>

    <div>
      <button class="btn btn-primary" v-on:click="getReferralLink()">
        Referral Link
      </button>
    </div>
    <br />
    <div>
      <button class="btn btn-primary" v-on:click="showListingCreate()">
        New Listing
      </button>
    </div>
    <div>
      <div class="row row-cols-1 row-cols-md-3 g-4">
        <div v-for="listing in group.listings" v-bind:key="listing.id">
          <div class="col">
            <div class="card text-center">
              <div class="card-header">
                <a :href="`${listing.url}`" target="_blank"
                  >{{ listing.address }}
                </a>
                <span
                  v-if="listing.status === `Confirmed`"
                  class="badge badge-success"
                  >{{ listing.status }}</span
                >
                <span
                  v-else-if="listing.status === `Denied`"
                  class="badge badge-danger"
                  >Denied</span
                >
                <span v-else class="badge badge-default">{{
                  listing.status
                }}</span>
              </div>
              <!-- / card-header -->
              <div class="card-body">
                <h4 class="card-title">
                  <a :href="`${listing.url}`" target="_blank">
                    <img
                      class="card-img-top"
                      :src="`${listing.image}`"
                      :alt="`${listing.address}`"
                    />
                  </a>
                </h4>
                <h6 class="card-text">
                  <span v-if="listing.beds">Beds: {{ listing.beds }}, </span>
                  <span v-if="listing.baths">Baths: {{ listing.baths }}, </span>
                  <span v-if="listing.squarefeet"
                    >Square Feet: {{ listing.squarefeet }},
                  </span>
                  <span v-if="listing.rent">Rent: {{ listing.rent }} </span>
                </h6>

                <div>
                  <div
                    v-for="comment in listing.comments"
                    v-bind:key="comment.id"
                  >
                    <span>{{ comment.user.name }}: {{ comment.text }} </span>
                    <button
                      class="btn btn-secondary btn-sm"
                      v-on:click="commentDelete(comment, listing)"
                      v-if="comment.user.id === currentUser.id"
                    >
                      delete
                    </button>
                  </div>
                </div>
                <br />
                <input v-model="newCommentParams.text" />
                &nbsp;
                <button
                  class="btn btn-secondary btn-sm"
                  v-on:click="commentCreate(listing)"
                >
                  Add Comment
                </button>
                <br />
                <br />
                <button
                  class="btn btn-secondary"
                  v-on:click="showListing(listing)"
                >
                  Edit Listing
                </button>
                &nbsp;
                <button
                  class="btn btn-secondary"
                  v-on:click="listingDelete(listing)"
                >
                  Delete Listing
                </button>
              </div>
              <!-- / card-body -->
            </div>
            <!-- / card -->
          </div>
        </div>
      </div>
      <dialog id="group-edit">
        <form method="dialog">
          <p>Name: <input type="text" v-model="editGroupParams.name" /></p>
          <p>Image: <input type="text" v-model="editGroupParams.image" /></p>
          <button class="btn btn-secondary" v-on:click="groupUpdate()">
            Update
          </button>
          <button class="btn btn-secondary">Close</button>
        </form>
      </dialog>
      <dialog id="listing-edit">
        <form method="dialog">
          <p>
            Beds:
            <input
              type="number"
              min="1"
              max="10"
              v-model="currentListing.beds"
            />
          </p>
          <p>
            Baths:
            <input
              type="number"
              min="1"
              max="10"
              v-model="currentListing.baths"
            />
          </p>
          <p>Area: <input v-model="currentListing.squarefeet" /></p>
          <p>Rent: <input v-model="currentListing.rent" /></p>
          <p>Image: <input v-model="currentListing.image" /></p>
          <p>URL: <input v-model="currentListing.url" /></p>
          <p>
            Status:
            <input
              type="text"
              v-model="currentListing.status"
              list="statuses"
            />
          </p>
          <p>{{ currentListing }}</p>
          <button
            class="btn btn-secondary"
            v-on:click="listingUpdate(currentListing)"
          >
            Update
          </button>
          <button class="btn btn-secondary">Close</button>
        </form>
      </dialog>
      <dialog id="listing-create">
        <form method="dialog">
          <p>Address: <input v-model="newListingParams.address" /></p>
          <p>
            Beds:
            <input
              type="number"
              min="1"
              max="10"
              v-model="newListingParams.beds"
            />
          </p>
          <p>
            Baths:
            <input
              type="number"
              min="1"
              max="10"
              v-model="newListingParams.baths"
            />
          </p>
          <p>Area: <input v-model="newListingParams.squarefeet" /></p>
          <p>Rent: <input v-model="newListingParams.rent" /></p>
          <p>Image: <input v-model="newListingParams.image" /></p>
          <p>URL: <input v-model="newListingParams.url" /></p>
          <p>
            Status:
            <input
              type="text"
              v-model="currentListing.status"
              list="statuses"
            />
          </p>
          <p>{{ newListingParams }}</p>
          <button class="btn btn-secondary" v-on:click="listingCreate()">
            Update
          </button>
          <button class="btn btn-secondary" v-on:click="newListingParams = {}">
            Close
          </button>
        </form>
      </dialog>
    </div>
    <section id="team" class="big-section">
      <div class="container">
        <div class="row row-cols-1 row-cols-md-3 g-4">
          <div v-for="user in group.users" v-bind:key="user.id">
            <div class="col">
              <div class="team block image-block">
                <div class="team-item">
                  <img :src="`${user.image}`" alt="" class="team-image" />
                </div>
                <!-- / team-item -->
                <div class="team-content">
                  <h5 class="box-title mb-1">{{ user.name }}</h5>
                  <p class="team-info mb-2 text-primary">{{ user.email }}</p>
                </div>
                <!-- / team-content -->
              </div>
              <!-- / image-block -->
            </div>
            <!-- / column -->
          </div>
        </div>
        <!-- / row -->
      </div>
      <!-- / container -->
    </section>
    <div>
      <button class="btn btn-warning" v-on:click="groupDelete()">
        Delete Group?
      </button>
    </div>
  </div>
</template>

<style>
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
      newCommentParams: {},
      statuses: [
        "Not Opened",
        "Pending Walkthrough",
        "Application Submitted",
        "Screening In Progress",
        "Screening Completed",
        "Additional Information Requested",
        "Confirmed",
        "Denied",
      ],
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
          this.newListingParams = {};
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    listingUpdate: function (listing) {
      axios
        .patch(`/listings/${listing.id}`, listing)
        .then((response) => {
          console.log(response.data);
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
    showListingCreate: function () {
      document.querySelector("#listing-create").showModal();
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
