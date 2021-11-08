<template>
  <div id="app">
    <nav class="navbar custom-menu navbar-expand-lg navbar-light bg-light">
      <div class="container">
        <a class="navbar-brand" href="/">Appartamento</a>

        <button
          class="navbar-toggler collapsed"
          type="button"
          data-toggle="collapse"
          data-target="#navbar-toggle"
          aria-controls="navbar-toggle"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="icon-bar top-bar"></span>
          <span class="icon-bar middle-bar"></span>
          <span class="icon-bar bottom-bar"></span>
          <span class="sr-only">Toggle navigation</span></button
        ><!-- / navbar-toggler -->

        <div class="collapse navbar-collapse" id="navbar-toggle">
          <ul class="navbar-nav ml-auto">
            <li v-if="!isLoggedIn()" class="nav-item">
              <a class="nav-link active" href="/login">login</a>
            </li>
            <li v-if="!isLoggedIn()" class="nav-item">
              <a class="nav-link" href="/signup">signup</a>
            </li>
            <li v-if="isLoggedIn()" class="nav-item">
              <a class="nav-link active" href="/groups/current">group</a>
            </li>
            <li v-if="isLoggedIn()" class="nav-item">
              <a class="nav-link last-menu-item" href="/users/current/edit"
                >my profile</a
              >
            </li>
            <li v-if="isLoggedIn()" class="nav-item">
              <a class="nav-link last-menu-item" href="/logout">logout</a>
            </li>
          </ul>
          <!-- / navbar-nav -->
        </div>
        <!-- / navbar-collapse -->
      </div>
      <!-- / container -->
    </nav>
    <router-view />
  </div>
</template>

<style></style>

<script>
export default {
  data: function () {
    return {};
  },
  methods: {
    isLoggedIn: function () {
      if (localStorage.getItem("jwt")) {
        return true;
      }
      return false;
    },
  },
};
</script>
