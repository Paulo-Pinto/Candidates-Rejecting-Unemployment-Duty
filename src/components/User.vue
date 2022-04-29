<template>
  <div class="container">
    <h1>{{ "id" }}ª pessoa : {{ name }}, {{ age }} yo from {{ country }}</h1>
  </div>
</template>

<script>
export default {
  name: "User",
  props: {
    id: Number,
    age: Number,
    name: {
      type: String,
      default: "Anonymous",
    },
    country: String,
  },
};
</script>

<style>
h1 {
  color: #42b983;
}
</style>
