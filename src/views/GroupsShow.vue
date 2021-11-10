<template>
  <div class="groups-show">
    <datalist id="statuses">
      <option v-for="status in statuses" v-bind:key="status">
        {{ status }}
      </option>
    </datalist>
    <header
      class="header-home2 parallax"
      :style="{
        backgroundImage: 'url(' + `${group.image}` + ')',
      }"
    >
      <div class="container">
        <div class="header-content text-center">
          <h1 class="header-title bg-transparent" style="color: white">
            {{ group.name }}
          </h1>
        </div>
      </div>
      <!-- / container -->
    </header>
    <br />
    <div>
      <button
        type="button"
        class="btn btn-secondary btn-sm mr-1"
        data-toggle="modal"
        data-target=".group-edit"
      >
        Edit Group
      </button>
    </div>
    <!-- medium modal -->
    <div class="modal fade group-edit" tabindex="-1" role="dialog">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Edit Group</h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">×</span>
            </button>
          </div>
          <!-- / modal-header -->
          <div class="modal-body">
            <form v-on:submit.prevent="submit()">
              <p>Name: <input type="text" v-model="editGroupParams.name" /></p>
              <p>
                Image:
                <input type="text" v-model="editGroupParams.image" />
              </p>
            </form>
          </div>
          <!-- / modal-body -->
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-dismiss="modal"
              v-on:click="closeGroup()"
            >
              Close
            </button>
            <button
              type="button"
              class="btn btn-primary"
              v-on:click="groupUpdate()"
              data-dismiss="modal"
            >
              Save changes
            </button>
          </div>
          <!-- / modal-footer -->
        </div>
        <!-- / modal-content -->
      </div>
      <!-- modal-dialog -->
    </div>
    <!-- / modal -->
    <!-- / medium modal -->
    <div id="map"></div>
    <br />
    <div>
      <button
        type="button"
        class="btn btn-primary mr-1"
        data-toggle="modal"
        data-target=".listing-create"
      >
        New Listing
      </button>
    </div>
    <!-- / medium modal -->
    <!-- medium modal -->
    <div class="modal fade listing-create" tabindex="-1" role="dialog">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">New Listing</h5>
            <button
              type="button"
              class="close"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">×</span>
            </button>
          </div>
          <!-- / modal-header -->
          <div class="modal-body">
            <form v-on:submit.prevent="submit()">
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
                  v-model="newListingParams.status"
                  list="statuses"
                />
              </p>
            </form>
          </div>
          <!-- / modal-body -->
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-dismiss="modal"
              v-on:click="closeListingNew()"
            >
              Close
            </button>
            <button
              type="button"
              class="btn btn-primary"
              v-on:click="listingCreate()"
              data-dismiss="modal"
            >
              Save changes
            </button>
          </div>
          <!-- / modal-footer -->
        </div>
        <!-- / modal-content -->
      </div>
      <!-- modal-dialog -->
    </div>
    <!-- / modal -->
    <!-- / medium modal -->
    <ul>
      <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
    </ul>
    <div class="container inner-space -2x pt-0">
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
                <span
                  v-else-if="listing.status === `Not Opened`"
                  class="badge badge-warning"
                  >Not Opened</span
                >
                <span
                  v-else-if="
                    listing.status === `Additional Information Requested`
                  "
                  class="badge badge-info"
                  >Additional Information Requested</span
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
                  <span v-if="listing.baths"
                    >Baths: {{ listing.baths.toString().replace(/\.0$/, "") }}
                  </span>
                  <br />
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
                      class="btn btn-sm"
                      v-on:click="commentDelete(comment, listing)"
                      v-if="comment.user.id === currentUser.id"
                    >
                      delete
                    </button>
                  </div>
                </div>
                <div v-if="activeCommentListingId === listing.id">
                  <input v-model="newCommentParams.text" />
                  &nbsp;
                  <button
                    class="btn btn-sm"
                    v-on:click="commentCreate(listing)"
                  >
                    Save
                  </button>
                  <button class="btn btn-sm" v-on:click="closeComment()">
                    Cancel
                  </button>
                </div>
                <div>
                  <button
                    class="btn btn-sm"
                    v-on:click="showComment(listing)"
                    v-if="activeCommentListingId != listing.id"
                  >
                    New Comment
                  </button>
                </div>
                <br />
                <button
                  v-on:click="listingShow(listing)"
                  type="button"
                  class="btn btn-secondary"
                  data-toggle="modal"
                  data-target=".listing-edit"
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
      <!-- medium modal -->
      <div class="modal fade listing-edit" tabindex="-1" role="dialog">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">Listing Edit</h5>
              <button
                type="button"
                class="close"
                data-dismiss="modal"
                aria-label="Close"
              >
                <span aria-hidden="true">×</span>
              </button>
            </div>
            <!-- / modal-header -->
            <div class="modal-body">
              <form v-on:submit.prevent="submit()">
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
              </form>
            </div>
            <!-- / modal-body -->
            <div class="modal-footer">
              <button
                type="button"
                class="btn btn-secondary"
                data-dismiss="modal"
                v-on:click="closeListingShow()"
              >
                Close
              </button>
              <button
                type="button"
                class="btn btn-primary"
                v-on:click="listingUpdate(currentListing)"
                data-dismiss="modal"
              >
                Save changes
              </button>
            </div>
            <!-- / modal-footer -->
          </div>
          <!-- / modal-content -->
        </div>
        <!-- modal-dialog -->
      </div>
      <!-- / modal -->
    </div>
    <section id="team" class="big-section">
      <div class="container">
        <div class="row row-cols-1 row-cols-md-3 g-4">
          <div v-for="user in group.users" v-bind:key="user.id">
            <div class="col">
              <div class="team block image-block">
                <div class="team-item">
                  <img
                    :src="`${user.image}`"
                    :alt="`${user.name}`"
                    class="team-image"
                  />
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
      <button class="btn btn-primary" v-on:click="getReferralLink()">
        Referral Link
      </button>
    </div>
    <br />
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
.groups-show {
  text-align: center;
}
.team-image {
  display: block;
  object-fit: cover;
  max-height: 350px;
  width: 100%;
}
#map {
  height: 500px;
  display: block;
}
</style>

<script>
/* global MapboxGeocoder */
import mapboxgl from "mapbox-gl";
import axios from "axios";
export default {
  data: function () {
    return {
      group: {},
      errors: [],
      currentListing: {},
      oldListing: {},
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
      activeCommentListingId: 0,
      places: [],
      startingCity: "",
    };
  },
  mounted: function () {
    // mapbox
    mapboxgl.accessToken = process.env.VUE_APP_MAPBOX_ACCESS_TOKEN;
    //makes a map
    const map = new mapboxgl.Map({
      container: "map", // container ID
      style: "mapbox://styles/mapbox/streets-v11", // style URL
      center: [-118.24, 34.05], // starting position [lng, lat]
      zoom: 12, // starting zoom
    });
    map.addControl(
      new MapboxGeocoder({
        accessToken: mapboxgl.accessToken,
        mapboxgl: mapboxgl,
      })
    );
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
    setFile: function (event) {
      if (event.target.files.length > 0) {
        this.image_file = event.target.files[0];
      }
    },
    submit: function () {
      var formData = new FormData();
      formData.append("name", this.editGroupParams.name);
      formData.append("image_file", this.image_file);

      axios.patch("/groups/current", formData).then((response) => {
        console.log(response);
        this.editGroupParams = {};
        this.$refs.fileInput.value = "";
      });
    },
    getReferralLink: function () {
      alert(`http://localhost:8080/signup?group_id=${this.group.id}`);
    },
    groupUpdate: function () {
      this.errors = [];
      for (const attribute in this.editGroupParams) {
        if (this.editGroupParams[attribute] === "") {
          this.editGroupParams[attribute] = null;
        }
      }
      axios
        .patch("/groups/current", this.editGroupParams)
        .then((response) => {
          console.log(response.data);
          this.editGroupParams = {};
          this.group = response.data;
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    closeGroup: function () {
      this.editGroupParams = {};
    },
    groupDelete: function () {
      this.errors = [];
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
      this.errors = [];
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
    listingShow: function (listing) {
      for (const attribute in listing) {
        this.oldListing[attribute] = listing[attribute];
      }
      this.currentListing = listing;
    },
    closeListingShow: function () {
      for (const attribute in this.oldListing) {
        this.currentListing[attribute] = this.oldListing[attribute];
      }
    },
    closeListingNew: function () {
      this.newListingParams = {};
    },
    listingUpdate: function (listing) {
      this.errors = [];
      axios
        .patch(`/listings/${listing.id}`, listing)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    listingDelete: function (listing) {
      this.errors = [];
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
    showComment: function (listing) {
      this.activeCommentListingId = listing.id;
    },
    closeComment: function () {
      this.activeCommentListingId = 0;
      this.newCommentParams.text = ``;
    },
    commentCreate: function (listing) {
      this.errors = [];
      this.newCommentParams.listing_id = listing.id;
      axios
        .post("/comments", this.newCommentParams)
        .then((response) => {
          console.log(response.data);
          this.newCommentParams.text = "";
          this.activeCommentListingId = 0;
          listing.comments.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    commentDelete: function (comment, listing) {
      this.errors = [];
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
