<template>
  <img alt="Vue logo" src="./assets/logo.png" />

  <div class="buttons">
    <button @click="create()">Create</button>
    <button @click="edit()">Change</button>
    <button @click="this.list.pop()">Pop</button>
    <button @click="this.list.shift()">Pop 0</button>
  </div>

  <div class="slidecontainer">
    <input
      v-model="sliderVal"
      type="range"
      min="1"
      max="100"
      class="slider"
      id="slider"
    />
  </div>

  <div class="list">
    <ul>
      <li v-for="x in list" :key="x">{{ x }}</li>
    </ul>
  </div>

  <div class="disabled">
    <input v-model="name" placeholder="Name" />
    <input v-model="location" placeholder="Location" />
    <input v-model="age" placeholder="Age" />
  </div>

  <select size="5" v-model="selected" @click="log(this.selected)">
    <option v-for="x in list">{{ x }}</option>
  </select>
</template>

<script>
// https://randomapi.com/documentation#options
const API_URL = `https://randomuser.me/api/?results=3`;

export default {
  name: "App",
  data() {
    return {
      list: ["sadsad", 2, 3],
      sliderVal: "",
      selected: "",
      name: "",
      location: "",
      age: "",
      vals: "",
      data: "",
    };
  },
  created() {
    // fetch on init
    this.data = this.fetchData();

    // for (x in data) {
    //   console.log("x");
    //   console.log(x);
    // }
  },
  mounted() {},
  methods: {
    create() {
      this.list.push(this.list.length + 1);
    },
    edit() {
      this.list[0] = this.sliderVal;
    },
    delete(ele) {
      this.list.remove(ele);
    },
    log(obj) {
      console.log(obj);
    },
    async fetchData() {
      this.vals = await (await fetch(API_URL)).json();
      console.log(this.vals);
    },
  },
  watch: {
    // "watches" anytime selected variable changes state
    selected(element) {
      // [this.name, this.age] = element.split(', ')
      this.name = element;
    },
  },
};
</script>

<style>
/* TODO : Bootstrap */
#app {
  align-items: center;
}

.list {
  color: blueviolet;
  display: flex;
  justify-content: center;
}

.buttons {
  display: flex;
  justify-content: center;
}

.disabled {
  display: flex;
  justify-content: center;
  align-items: center;
  pointer-events: none;
}

img {
  margin: 10px;
  display: block;
  margin-left: auto;
  margin-right: auto;
}
select {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 14em;
}
</style>
