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
			class="bg-white border-top-0 border-info rounded-bottom p-3 shadow card"
			v-for="person in Object.values(list)"
			:key="person"
			:id="person.name"
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
			// stores dictionary of people
			list: {},
			// stores key of curr focused person
			selected: '',
			// stores person's values
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
		window.setInterval(() => {
			console.log('People -> ' + Object.keys(this.list));
		}, 3000);
	},
	methods: {
		update() {
			// replace values
			this.list[this.name].image = this.image;
			this.list[this.name].name = this.name;
			this.list[this.name].age = this.age;
			this.list[this.name].country = this.country;
			this.list[this.name].city = this.city;

			// this.list[this.name] = {
			// 	image: this.image,
			// 	name: this.name,
			// 	age: this.age,
			// 	country: this.country,
			// 	city: this.city,
			// };
			// TODO : update selected
			// this.selected = p;

			// TODO : UPDATE ISNT WORKING
			// temp fix
			this.resetHardcode();
		},
		resetEdit() {
			this.$refs.editForm.reset();
		},
		resetHardcode() {
			this.image = '';
			this.name = '';
			this.age = '';
			this.country = '';
			this.city = '';
			this.$refs.editForm.style['pointer-events'] = 'none';
		},
		remove() {
			this.resetHardcode();
			delete this.list[this.selected.name];
		},
		select(person) {
			// attributes to display on input boxes
			[this.image, this.name, this.age, this.country, this.city] =
				Object.values(person);
			// save selected person's key
			this.selected = person;
			// enable inputs
			this.$refs.editForm.style['pointer-events'] = 'auto';
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

				// delete for no dupes
				delete this.list[key];
			}
			// replace list
			this.list = new_list;
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
			let pos = 0;

			// add n skills
			for (
				let n = 0;
				n < 1 + Math.floor(Math.random() * (skills.length - 1));
				n++
			) {
				// if skill has already been chosen, select new one
				pos = Math.floor(Math.random() * skills.length);
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
				// select and display new user
				[this.image, this.name, this.age, this.country, this.city] =
					Object.values(p);
				this.selected = p.name;
			});
		},
	},
	watch: {
		// "watches" anytime selected variable changes state
		// selected(element) {
		// 	[this.image, this.name, this.age, this.country, this.city] =
		// 		Object.values(this.list[element]);
		// },
	},
};
</script>

<style>
/* TODO : Fix narrative */
#app {
	align-items: center;
	padding: 50px;
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

// TODO : change to grid view, click opens details details allows for
editing/deleting vue-meta virt scroll
https://github.com/rocwang/vue-virtual-scroll-grid#readme
