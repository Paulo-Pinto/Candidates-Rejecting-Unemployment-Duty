// TODO : moment.js clock for each user's timezone
<template>
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

	<div class="d-flex justify-content-center">
		<button class="mt-4 mx-1 btn btn-success" @click="fetchPeople(1)">
			New Candidate
		</button>
		<button class="mt-4 mx-1 btn btn-warning" @click="update()">
			Update
		</button>
		<button class="mt-4 mx-1 btn btn-danger" @click="remove()">
			Remove
		</button>
		<!-- TODO : Shuffle / Sort button -->

		<!-- <button class="mt-4 mx-1 btn btn-info" @click="sort(how?)">
			Sort
		</button> -->
		<button class="mt-4 mx-1 btn btn-dark" @click="shuffle()">
			Shuffle
		</button>
	</div>

	<form
		ref="editForm"
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

	<!-- all users -->
	<!-- <select :size="Object.keys(this.list).length" v-model="selected"> -->
	<!-- <select :size="3" v-model="selected">
		<option v-for="x in Object.keys(list)" :key="x">{{ x }}</option>
	</select> -->

	<!-- user box -->
	<!-- <div id="user" class="position-absolute top-150 start-50 col text-center">
		<div
			id="border"
			class="border border-top-0 border-info rounded-bottom p-4"
		>
			<h1 id="name" class="display-4 text-info"></h1>
			<h2 id="username" class="mb-3"></h2>

			<img
				alt="User Image"
				:src="this.image"
				class="border border-info mb-3"
			/>
			<ul id="personal_info" class="list-group">
				<li
					class="list-group-item"
					v-for="ele in [
						this.name,
						this.age,
						this.city,
						this.country,
					]"
					:key="ele"
				>
					{{ ele }}
				</li>
			</ul>
		</div>
		<div class="buttons">
			<button class="mt-4 mx-2 btn btn-info" @click="fetchPeople(1)">
				Create
			</button>
			<button class="mt-4 mx-2 btn btn-info" @click="update()">
				Update
			</button>
			<button class="mt-4 mx-2 btn btn-info" @click="remove()">
				Remove
			</button>
			<button class="mt-4 mx-2 btn btn-info" @click="resetEdit()">
				Reset
			</button>
		</div>
	</div> -->

	<br />
	<br />
	<!-- grid div" -->
	<div class="grid">
		<!-- TODO : this div might be removeable -->
		<!-- user box -->
		<div
			v-for="person in Object.values(list)"
			:key="person"
			:class="person.hover == true ? 'hovered' : 'notHovered'"
			@click="this.select(person)"
			@mouseenter="person.hover = true"
			@mouseleave="person.hover = false"
			:id="person.name"
			class="bg-white border border-top-0 border-info rounded-bottom p-3 shadow item"
		>
			<h2 id="username" class="mb-3">
				{{ person.name }}
			</h2>

			<img
				alt="User Image"
				:src="person.image"
				class="border border-info mb-3"
			/>
			<ul id="personal_info" class="list-group">
				<li
					class="list-group-item"
					v-for="ele in [
						person.age + '€ / day',
						person.city + ', ' + person.country,
						Object.values(person.skills).join(' and '),
					]"
					:key="ele"
				>
					{{ ele }}
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
			//grid
			gridLength: 0,
			// hover
			hover: false,
		};
	},
	// runs on init
	created() {
		this.fetchPeople(2);
	},
	methods: {
		update() {
			// delete old entry
			// delete this.list[this.name];
			// add updated entry
			this.list[this.name] = {
				image: this.image,
				name: this.name,
				age: this.age,
				country: this.country,
				city: this.city,
			};
			// TODO : update selected
			// this.selected = p;

			// TODO : UPDATE ISNT WORKING
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
			this.image = '';
			this.name = '';
			this.age = '';
			this.country = '';
			this.city = '';
		},
		remove() {
			this.resetHardcode();
			delete this.list[this.selected.name];
		},
		select(person) {
			[this.image, this.name, this.age, this.country, this.city] =
				Object.values(person);
			// console.log(Object.values(person));
			this.selected = person;
		},
		shuffle() {
			let new_list = this.list;
			const initial_len = this.list.length;
			console.log('old');
			console.log(this.list);

			// shuffle list
			for (let i = 0; i < initial_len; i++) {
				// get random position
				pos = Math.floor(Math.random() * this.list.length);
				// change person from one list to the next
				new_list.push(this.list[pos]);
				delete this.list[pos];
			}
			// update list
			tihs.list = new_list;
			console.log('new');
			console.log(this.list);
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
		async fetchPeople(quant = 1) {
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
				};

				this.list[p.name] = p;
				this.gridLength++;
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
/* TODO : Bootstrap */
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

.grid {
	display: grid;
	/* grid-gap: 20px; */
	grid-template-columns: repeat(4, 1fr);
	grid-template-rows: repeat(1, 1fr);
	grid-column-gap: 10px;
	grid-row-gap: 10px;
}

.item {
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
