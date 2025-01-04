<template>
  <div>
    <ul class="nav nav-pills">
      <li class="nav-item">
        <router-link class="nav-link" active-class="active" to="/nested/one"
          >Nested One</router-link
        >
      </li>
      <li class="nav-item">
        <router-link class="nav-link" active-class="active" to="/nested/two"
          >Nested two</router-link
        >
      </li>
    </ul>
    <hr class="my-4" />
    <RouterView></RouterView>
  </div>
</template>

<script>
export default {};
</script>

<style lang="scss" scoped></style>
