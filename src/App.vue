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
    <input v-model="age" placeholder="Age" />
    <input v-model="country" placeholder="Country" />
    <input v-model="city" placeholder="City" />
  </div>

  <select size="5" v-model="selected" @click="log(this.selected)">
    <option v-for="x in list">{{ x }}</option>
  </select>
</template>

<script>
// https://randomapi.com/documentation#options
const API_URL_GET_3 = `https://randomuser.me/api/?results=3`;
const API_URL = `https://randomuser.me/api/`;

export default {
  name: "App",
  data() {
    return {
      list: {},
      sliderVal: "",
      selected: "",
      name: "",
      age: "",
      city: "",
      country: "",
      vals: "",
      data: "",
    };
  },
  created() {
    // fetch on init
    this.data = this.fetchData();
  },
  mounted() {},
  methods: {
    create() {
      this.fetchOne();
    },
    edit() {
      this.list[0] = this.sliderVal;
    },
    delete(ele) {
      delete this.list[ele];
    },
    log(obj) {
      console.log(obj);
    },
    async fetchData() {
      this.vals = await (await fetch(API_URL_GET_3)).json();
      console.log(this.vals);

      // for (let i = 0; i < 3; i++) {
      //   var x = this.vals.results[i];
      this.vals.results.forEach((x) => {
        const p = {
          name: x.name.first + " " + x.name.last,
          age: x.dob.age,
          country: x.location.country,
          city: x.location.city,
        };

        this.list[p.name] = p;
      });
    },
    async fetchOne() {
      var call = await (await fetch(API_URL)).json();

      call.results.forEach((x) => {
        const p = {
          name: x.name.first + " " + x.name.last,
          age: x.dob.age,
          country: x.location.country,
          city: x.location.city,
        };

        this.list[p.name] = p;
      });
    },
  },
  watch: {
    // "watches" anytime selected variable changes state
    selected(element) {
      let a = element.split(", ");
      console.log(a);
      this.name = element.name;
      this.age = element.age;
      this.country = element.country;
      this.city = element.city;
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
  width: 24em;
}
</style>
