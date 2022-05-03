<template>
	<img alt="Vue logo" src="./assets/logo.png" />

	<div class="buttons">
		<button @click="fetchPeople(1)">Create</button>
		<button @click="edit()">Change</button>
		<button @click="remove()">Remove</button>
		<button @click="resetEdit()">Reset</button>
	</div>
	<form :class="this.selected == '' ? 'disabled' : 'abled'" ref="editForm">
		<input v-model="name" placeholder="Name" />
		<input v-model="age" placeholder="Age" type="number" />
		<input v-model="city" placeholder="City" />

		<input v-model="country" list="countries" placeholder="Country" />
		<datalist id="countries">
			<v-for v-for="country in this.countries_list" :key="country">
				<option>{{ country.name }}</option>
			</v-for>
		</datalist>
	</form>

	<select :size="Object.keys(this.list).length" v-model="selected">
		<option v-for="x in Object.keys(list)" :key="x">{{ x }}</option>
	</select>
</template>

<script>
import { countries } from 'country-list-json';

// https://randomapi.com/documentation#options
const API_URL = `https://randomuser.me/api/`;

export default {
	name: 'App',
	data() {
		return {
			// stores dictionary of people
			list: {},
			// stores key of curr focused person
			selected: '',
			// stores person's values
			name: '',
			age: '',
			city: '',
			country: '',
			// countries
			countries_list: countries,
		};
	},
	// runs on init
	created() {
		this.fetchPeople(3);
	},
	methods: {
		edit() {
			// delete old entry
			delete this.list[this.selected];
			// add updated entry
			this.list[this.name] = {
				name: this.name,
				age: this.age,
				country: this.country,
				city: this.city,
			};
			// TODO : update selected
			// this.selected = p;

			// temp fix
			this.resetHardcode();
		},
		delete(ele) {
			delete this.list[ele];
		},
		log(obj) {
			console.log(obj);
		},
		resetEdit() {
			this.$refs.editForm.reset();
		},
		resetHardcode() {
			this.name = '';
			this.age = '';
			this.country = '';
			this.city = '';
		},
		remove() {
			// TODO : only one command executes !?
			// this.$refs.editForm.reset();
			this.resetHardcode();
			delete this.list[this.selected];
		},
		async fetchPeople(quant = 1) {
			let vals = await (
				await fetch(API_URL + '?results=' + quant)
			).json();

			vals.results.forEach((x) => {
				const p = {
					name: x.name.first + ' ' + x.name.last,
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
			this.selected = element;

			[this.name, this.age, this.country, this.city] = Object.values(
				this.list[element]
			);
		},
	},
};
</script>

<style>
/* TODO : Bootstrap */
#app {
	align-items: center;
	padding: 50px;
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

.abled {
	display: flex;
	justify-content: center;
	align-items: center;
	color: red;
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
