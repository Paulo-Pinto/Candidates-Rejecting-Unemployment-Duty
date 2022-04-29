<template lang="">
  <div>
    <p>Message is: {{ search }}</p>
    <p>Message is: {{ this.lower(search) }}</p>
    <p>Message is: {{ this.upper(search) }}</p>
    <p v-if="this.lower(search) === 'hello'">World</p>
    <input v-model="search" id="search" />
  </div>
</template>
<script>
export default {
  props: {},
  data() {
    return {
      search: "",
    };
  },
  methods: {
    lower: function (str) {
      return str.toLowerCase();
    },
    upper: function (str) {
      return str.toUpperCase();
    },
  },
};
</script>
<style lang=""></style>
