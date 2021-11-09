<template>
  <div class="home">
    <header class="page-header">
      <div class="container">
        <div class="header-content text-center">
          <div class="title-background">
            <h1 class="header-title font-light">{{ message }}</h1>
          </div>
          <!-- / title-background -->
        </div>
      </div>
      <!-- / container -->
    </header>
  </div>
</template>

<style></style>

<script>
export default {
  data: function () {
    return {
      message: "Welcome to Appartamento!",
    };
  },
  created: function () {},
  methods: {},
};
</script>
