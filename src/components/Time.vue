<template lang="">
  <div>
    <p>The time is {{ this.time }}</p>
  </div>
  <div>
    <p>{{ this.up }}{{ search }}</p>
  </div>
</template>

<script>
export default {
  name: "Time",
  props: {},
  created() {
    this.start();
  },
  updated() {
    this.up = "Updated";
  },
  methods: {
    start() {
      this.time = Math.round(+new Date() / 1000);
    },
  },
};
</script>

<style lang=""></style>
