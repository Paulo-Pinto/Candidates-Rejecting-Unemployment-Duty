// TODO : moment.js clock for each user's timezone
<template>
	<!-- https://stackoverflow.com/a/52483232 -->
	<link
		href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css"
		rel="stylesheet"
		integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC"
		crossorigin="anonymous"
	/>

	<h1 class="display-1 d-flex justify-content-center">
		Citizens Rejecting Unemployment Duty&nbsp;
		<span class="text-primary">(CRUD)</span>
	</h1>

	<!-- Buttons -->
	<div class="d-flex justify-content-center">
		<button class="mt-4 mx-1 btn btn-success" @click="fetchPeople()">
			More Candidates
		</button>
		<button class="mt-4 mx-1 btn btn-warning" @click="update()">
			Update
		</button>
		<button class="mt-4 mx-1 btn btn-danger" @click="remove()">
			Remove
		</button>
		<button class="mt-4 mx-1 btn btn-dark" @click="shuffle()">
			Shuffle
		</button>
		<!-- TODO : Sort button(s?) -->
	</div>

	<!-- Input fields -->
	<form
		ref="editForm"
		id="editForm"
		class="input-group mt-4 d-flex justify-content-center px-5"
	>
		<div class="input-group-prepend">
			<span class="input-group-text">Name</span>
		</div>
		<input
			class="form-control border-secondary rounded-end"
			v-model="name"
			placeholder="Name"
		/>

		<div class="input-group-prepend ms-4">
			<span class="input-group-text">Age</span>
		</div>
		<input
			class="form-control border-secondary rounded-end"
			v-model="age"
			placeholder="Age"
			type="number"
		/>

		<div class="input-group-prepend ms-4">
			<span class="input-group-text">City</span>
		</div>
		<input
			class="form-control border-secondary rounded-end"
			v-model="city"
			placeholder="City"
		/>

		<div class="input-group-prepend ms-4">
			<span class="input-group-text">Country</span>
		</div>
		<input
			class="form-control border-secondary rounded-end"
			v-model="country"
			list="countries"
			placeholder="Country"
			type="text"
		/>
		<datalist id="countries">
			<option v-for="country in this.countries_list" :key="country">
				{{ country.name }}
			</option>
		</datalist>
	</form>

	<!-- Grid of People -->
	<div class="grid" v-if="Object.keys(list).length > 0">
		<!-- Person Card -->
		<div
			class="border-top-0 rounded-bottom p-3 shadow card"
			v-for="person in Object.values(list)"
			:key="person"
			:id="person.name"
			:ref="person.name"
			:class="person.hover == true ? 'hovered' : ''"
			@mouseenter="person.hover = true"
			@mouseleave="person.hover = false"
			@click="this.select(person)"
		>
			<h2 id="username" class="mb-4">
				{{ person.name }}
			</h2>

			<img
				alt="User Image"
				:src="person.image"
				class="border border-info mb-4"
			/>
			<ul id="personal_info" class="list-group">
				<li
					class="list-group-item"
					v-for="field in [
						person.age + '€ / day',
						person.city + ', ' + person.country,
						Object.values(person.skills).join(' and '),
					]"
					:key="field"
				>
					{{ field }}
				</li>
			</ul>
		</div>
	</div>
</template>

<script>
import { countries } from 'country-list-json';

// https://randomapi.com/documentation#options
const API_URL = `https://randomuser.me/api/`;

export default {
	name: 'App',
	// metaInfo: {
	// 	title: 'Vue CRUD SPA',
	// 	// meta: [
	// 	// 	{
	// 	// 		name: 'description',
	// 	// 		content:
	// 	// 			'Perform CRUD operations on the randomuser API. Made with Vue',
	// 	// 	},
	// 	// 	{
	// 	// 		name: 'keywords',
	// 	// 		content:
	// 	// 			'Vue, Vue3, HTML, CSS, Bootstrap, Javascript, JS, API, randomuser',
	// 	// 	},
	// 	// 	{
	// 	// 		name: 'author',
	// 	// 		content: 'Paulo Pinto',
	// 	// 	},
	// 	// ],
	// },
	data() {
		return {
			// dictionary of people
			list: {},
			// stores key of curr focused person, for deletion purposes
			selected: '',
			// stores focused person's values
			image: '',
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
		this.fetchPeople(2);
	},
	mounted: function () {
		setInterval(() => {
			console.log(
				Math.floor(new Date() / 1000) + ' -> ' + Object.keys(this.list)
			);
		}, 10000);
	},
	methods: {
		update() {
			// TODO : fix error when changing name
			// replace persons values
			this.list[this.name].image = this.image;
			this.list[this.name].name = this.name;
			this.list[this.name].age = this.age;
			this.list[this.name].country = this.country;
			this.list[this.name].city = this.city;

			// TODO : a better Toast is possible... see youtube's notification when adding to playlist
			alert(this.name + "'s values have been updated!");

			this.selected = ''; // update selected
			this.resetHardcode(); // reset form inputs
			this.highlightPerson(); // removes all highlights if none is selected
		},
		resetHardcode() {
			// remove input values
			this.image = '';
			this.name = '';
			this.age = '';
			this.country = '';
			this.city = '';
			// disable inputs
			this.$refs.editForm.style['pointer-events'] = 'none';
		},
		remove() {
			this.resetHardcode(); // reset form inputs
			delete this.list[this.selected.name]; // remove person
		},
		select(person) {
			// attributes to display on input boxes
			[this.image, this.name, this.age, this.country, this.city] =
				Object.values(person);
			// save selected person's key
			this.selected = person;
			// enable inputs
			this.$refs.editForm.style['pointer-events'] = 'auto';

			this.highlightPerson(this.name);
		},
		highlightPerson(name) {
			for (const [key, value] of Object.entries(this.$refs)) {
				// add border color if key is selected person
				value[0].style['border-color'] = key == name ? '#5bc0de' : '';
				value[0].style['border-width'] =
					key == name ? 'medium' : 'thin';
			}
		},
		shuffle() {
			// temp list
			let new_list = [];
			// length has to be consistent for the for loop to finish
			const initial_len = Object.keys(this.list).length;

			// shuffle list
			for (let i = 0; i < initial_len; i++) {
				// get random position
				let pos = Math.floor(
					Math.random() * Object.keys(this.list).length
				);
				// random key from list keys
				let key = Object.keys(this.list)[pos];
				new_list[key] = this.list[key];

				delete this.list[key]; // delete for no dupes
			}

			this.list = new_list; // replace list
		},
		genSkills() {
			const skills = [
				'Javascript',
				'Vue.js',
				'Bootstrap',
				'Python',
				'Kotlin',
				'Java',
				'C',
				'C++',
			];

			let build = [];

			// add n skills
			for (
				let n = 0;
				n < 1 + Math.floor(Math.random() * (skills.length - 1));
				n++
			) {
				// if skill has already been chosen, select new one
				let pos = Math.floor(Math.random() * skills.length);
				// remove skill from list and add it to return var
				build.push(skills.splice(pos, 1).toString());
			}
			return build;
		},
		async fetchPeople(quant = 2) {
			let vals = await (
				await fetch(API_URL + '?results=' + quant)
			).json();

			vals.results.forEach((x) => {
				const p = {
					image: x.picture.large,
					name: x.name.first + ' ' + x.name.last,
					age: x.dob.age,
					country: x.location.country,
					city: x.location.city,
					skills: this.genSkills(),
					hover: false,
				};

				this.list[p.name] = p;

				// fill input fields
				[this.image, this.name, this.age, this.country, this.city] =
					Object.values(p);

				this.selected = p.name;
			});
		},
	},
	watch: {
		// anytime selected variable changes state, run its assigned function
		// selected(element) {
		// [this.image, this.name, this.age, this.country, this.city] =
		// 				Object.values(person);
		// },
	},
};
</script>

<style>
#app {
	align-items: center;
	padding: 50px;
}

img {
	margin: 10px auto;
	display: block;
}

.grid {
	display: grid;
	padding-top: 3em;
	grid-template-columns: repeat(4, 1fr);
	grid-template-rows: repeat(1, 1fr);
	grid-column-gap: 1em;
	grid-row-gap: 2em;
}

.card {
	text-align: center;
}

.hovered {
	/* TODO : scales behind other divs */
	transform: scale(1.2);
	transition-duration: 0.25s;
	transition-timing-function: ease-out;
	z-index: 999;
}
</style>

// TODO : vue-meta,fix narrative
