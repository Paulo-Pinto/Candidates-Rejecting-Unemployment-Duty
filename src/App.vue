<template>
	<link
		href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css"
		rel="stylesheet"
		integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC"
		crossorigin="anonymous"
	/>
	<img alt="Vue logo" src="./assets/logo.png" />

	<form :class="this.selected == '' ? 'disabled' : 'abled'" ref="editForm">
		<input v-model="name" placeholder="Name" />
		<input v-model="age" placeholder="Age" type="number" />
		<input v-model="city" placeholder="City" />

		<input
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

	<div class="buttons">
		<button class="mt-4 mx-1 btn btn-info" @click="fetchPeople(1)">
			Create
		</button>
		<button class="mt-4 mx-1 btn btn-info" @click="update()">Update</button>
		<button class="mt-4 mx-1 btn btn-info" @click="remove()">Remove</button>
		<button class="mt-4 mx-1 btn btn-info" @click="resetEdit()">
			Reset
		</button>
	</div>

	<!-- all users -->
	<!-- <select :size="Object.keys(this.list).length" v-model="selected"> -->
	<select :size="3" v-model="selected">
		<option v-for="x in Object.keys(list)" :key="x">{{ x }}</option>
	</select>

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
	<!-- v-for="x in Object.keys(list)" -->
	<div :length="this.gridLength" class="grid">
		<div class="item" v-for="x in Object.keys(list)" :key="x">
			<!-- user box -->
			<div @click="this.select(x)" id="user">
				<div
					id="border"
					class="border border-top-0 border-info rounded-bottom p-4"
				>
					<h2 id="username" class="mb-3">
						<!-- {{ person['image'] }} -->
						__
					</h2>

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
			</div>
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
		};
	},
	// runs on init
	created() {
		this.fetchPeople(3);
	},
	methods: {
		update() {
			// delete old entry
			delete this.list[this.selected];
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
			// TODO : only one command executes !?
			// this.$refs.editForm.reset();
			this.resetHardcode();
			delete this.list[this.selected];
		},
		select(person) {
			[this.image, this.name, this.age, this.country, this.city] =
				Object.values(person);
			console.log(Object.values(person));
		},
		async fetchPeople(quant = 1) {
			let vals = await (
				await fetch(API_URL + '?results=' + quant)
			).json();
			console.log(vals.results);
			vals.results.forEach((x) => {
				const p = {
					image: x.picture.large,
					name: x.name.first + ' ' + x.name.last,
					age: x.dob.age,
					country: x.location.country,
					city: x.location.city,
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
		selected(element) {
			[this.image, this.name, this.age, this.country, this.city] =
				Object.values(this.list[element]);
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

.grid {
	display: grid;
	/* grid-gap: 20px; */
	grid-template-columns: repeat(4, 1fr);
	grid-template-rows: repeat(3, 1fr);
	grid-column-gap: 10px;
	grid-row-gap: 10px;
}

.item {
	/* border: 3px dashed yellow; */
	padding: 0px 0;
	height: fit-content;
	/* margin: auto; */
	text-align: center;
}
</style>

// TODO : change to grid view, click opens details details allows for
editing/deleting vue-meta virt scroll
https://github.com/rocwang/vue-virtual-scroll-grid#readme
