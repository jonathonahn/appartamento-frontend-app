<template>
  <div id="app">
    <div id="nav">
      <span v-if="!isLoggedIn()">
        <router-link to="/login">Login</router-link> |
        <router-link to="/signup">Signup</router-link>
      </span>
      <span v-if="isLoggedIn()">
        <router-link v-if="isLoggedIn()" to="/groups/current"
          >Group</router-link
        >
        |
        <router-link to="/users/current/edit">My Profile</router-link>
        |
        <router-link to="/logout">Logout</router-link>
      </span>
    </div>
    <nav class="navbar custom-menu navbar-expand-lg navbar-light bg-light">
      <div class="container">
        <a class="navbar-brand" href="/index4.html"
          ><img src="/images/logo.png" alt=""
        /></a>

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
            <li class="nav-item">
              <a class="nav-link active" href="/index4.html">HOME</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="/about4.html">ABOUT</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="/works.html">WORKS</a>
            </li>
            <li class="nav-item">
              <a class="nav-link last-menu-item" href="/contact4.html"
                >CONTACT</a
              >
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
