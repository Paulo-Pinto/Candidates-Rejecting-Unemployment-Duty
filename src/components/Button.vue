<template>
  <button
    :style="{ background: color }"
    class="btn"
    :src="href"
    @click="this.clicked()"
  >
    {{ text }}
  </button>
  <p id="abou"></p>
</template>

<script>
export default {
  name: "Button",
  props: {
    text: String,
    color: String,
    href: String,
  },
  changed() {
    console.log(search.value);
  },
  methods: {
    clicked() {
      this.$refs.abou.text = search.value;
      console.log("clicked");
      console.log(search.value);
    },
  },
};
</script>

<style lang=""></style>
