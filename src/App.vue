// TODO : moment.js clock for each user's timezone
<template>
	<div class="d-flex justify-content-center">
		<h1 class="display-1 d-none d-md-block collapse collapse-horizontal">
			Citizens Rejecting Unemployment Duty&nbsp;
		</h1>

		<span class="display-1 text-primary">(CRUD)</span>
	</div>

	<!-- Buttons -->
	<div
		class="justify-content-center row row-cols-2 row-cols-md-4 text-center"
	>
		<div class="col col-sm-auto">
			<button class="mt-4 btn btn-success px-3" @click="fetchPeople()">
				More Candidates
			</button>
		</div>

		<div class="col col-sm-auto">
			<button class="mt-4 btn btn-warning px-3" @click="update()">
				Update
			</button>
		</div>

		<div class="col col-sm-auto">
			<button class="mt-4 btn btn-danger px-3" @click="remove()">
				Remove
			</button>
		</div>

		<div class="col col-sm-auto">
			<button class="mt-4 btn btn-dark px-3" @click="shuffle()">
				Shuffle
			</button>
		</div>
	</div>

	<!-- Input fields -->
	<form
		ref="editForm"
		id="editForm"
		class="input-group row row-cols-1 row-cols-sm-2 row-cols-lg-4 px-5 my-4"
	>
		<div class="col d-flex my-2">
			<div class="input-group-prepend ms-4">
				<span class="input-group-text rounded-start-input">Name</span>
			</div>
			<input
				class="form-control border-secondary rounded-end-input"
				v-model="name"
				placeholder="Name"
				disabled="true"
			/>
		</div>

		<div class="col d-flex my-2">
			<!-- newer version can use inert -->
			<div class="input-group-prepend ms-4">
				<span class="input-group-text rounded-start-input">Wage</span>
			</div>
			<input
				class="form-control border-secondary rounded-end-input"
				v-model="wage"
				placeholder="Wage"
				type="number"
			/>
		</div>

		<div class="col d-flex my-2">
			<div class="input-group-prepend ms-4">
				<span class="input-group-text rounded-start-input">City</span>
			</div>
			<input
				class="form-control border-secondary rounded-end-input"
				v-model="city"
				placeholder="City"
			/>
		</div>

		<div class="col d-flex my-2">
			<div class="input-group-prepend ms-4">
				<span class="input-group-text rounded-start-input"
					>Country</span
				>
			</div>
			<input
				class="form-control border-secondary rounded-end-input"
				v-model="country"
				list="countries"
				placeholder="Country"
				type="text"
			/>
		</div>

		<datalist id="countries">
			<option v-for="country in this.countries_list" :key="country">
				{{ country.name }}
			</option>
		</datalist>
	</form>

	<!-- Grid of People -->
	<div
		class="row row-cols-auto justify-content-evenly"
		v-if="Object.keys(list).length > 0"
	>
		<!-- Person Card -->
		<div
			class="border-top-0 rounded-bottom p-3 shadow card flex-even my-3 my-md-2"
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
						person.wage + '€ / day', // illustrative value
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
	<!-- <div class="position-fixed bottom-0 p-3" style="z-index: 11">
		<div
			id="liveToast"
			class="toast"
			role="alert"
			aria-live="assertive"
			aria-atomic="true"
		>
			<div class="toast-header">
				<strong class="me-auto">Email copied!</strong>
				<button
					type="button"
					class="btn-close"
					data-bs-dismiss="toast"
					aria-label="Close"
				></button>
			</div>
			<div class="toast-body">Hope to be hearing from you soon :)</div>
		</div>
	</div>

	<div
		class="p-3 bg-secondary progress-bar-striped"
		style="min-height: 170px"
	>
		<b-button
			class="mb-2"
			variant="primary"
			@click="$bvToast.show('example-toast')"
		>
			Show toast
		</b-button>
		<b-toast id="example-toast" title="BootstrapVue" static no-auto-hide>
			Hello, world! This is a toast message.
		</b-toast>
	</div> -->
</template>

<script>
import { countries } from 'country-list-json';
import { BootstrapVue, IconsPlugin } from 'bootstrap-vue';
import $ from 'jquery';

// Import Bootstrap and BootstrapVue CSS files (order is important)
import 'bootstrap/dist/css/bootstrap.css';
import 'bootstrap-vue/dist/bootstrap-vue.css';

// https://randomapi.com/documentation#options
const API_URL = `https://randomuser.me/api/`;

export default {
	name: 'App',
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
			if (this.selected.name == undefined) {
				alert('No person selected to update!');
				return;
			}

			let p = this.selected['name'];

			// replace persons values
			this.list[p].image = this.image;
			this.list[p].wage = this.wage;
			this.list[p].country = this.country;
			this.list[p].city = this.city;
			// this.list[p].name = this.name;
			this.selected = ''; // update selected

			this.resetHardcode(); // reset form inputs
			this.highlightPerson(); // removes all highlights if none is selected
			// TODO : https://getbootstrap.com/docs/5.0/components/toasts/
			// alert(p + "'s values have been updated!");
		},
		resetHardcode() {
			// remove input values
			this.image = '';
			this.name = '';
			this.wage = '';
			this.country = '';
			this.city = '';

			// disable inputs
			this.$refs.editForm.style['pointer-events'] = 'none';
			// TODO : make inner inputs tabindex -1
			// this.$refs.editForm.style['tabindex'] = '-1';
		},
		remove() {
			this.resetHardcode(); // reset form inputs

			if (this.selected.name == undefined) {
				alert('No person selected to remove!');
				return;
			}

			delete this.list[this.selected.name]; // remove person
			this.selected = ''; // update selected
		},
		select(person) {
			// attributes to display on input boxes
			[this.image, this.name, this.wage, this.country, this.city] =
				Object.values(person);
			// save selected person's key
			this.selected = person;
			// enable inputs
			this.$refs.editForm.style['pointer-events'] = 'auto';

			this.highlightPerson(this.name);
			// console.log(this.selected);
		},
		highlightPerson(name) {
			for (const [key, value] of Object.entries(this.$refs)) {
				// add border color if key is selected person
				if (value[0] != undefined) {
					value[0].style['border-color'] =
						key == name ? '#5bc0de' : '';
					value[0].style['border-width'] =
						key == name ? 'medium' : 'thin';
				}
			}

			// show confirmation
			// $('#liveToast').toast('show');
		},
		shuffle() {
			// temp list
			let new_list = [];
			// length has to be consistent for the for loop to finish
			const initial_len = Object.keys(this.list).length;

			// shuffle list
			for (let i = 0; i < initial_len; i++) {
				// get random position
				const pos = Math.floor(
					Math.random() * Object.keys(this.list).length
				);
				// random key from list keys
				const key = Object.keys(this.list)[pos];
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
				const pos = Math.floor(Math.random() * skills.length);
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
					wage: x.dob.age,
					country: x.location.country,
					city: x.location.city,
					skills: this.genSkills(),
					hover: false,
				};
				// TODO - change key from person to unique id

				this.list[p.name] = p;

				// fill input fields
				[this.image, this.name, this.wage, this.country, this.city] =
					Object.values(p);

				this.selected = p.name;
			});
		},
	},
	watch: {
		// anytime selected variable changes state, run its assigned function
		// selected(element) {
		// [this.image, this.name, this.wage, this.country, this.city] =
		// 				Object.values(person);
		// },
	},
};

// var toastTrigger = document.getElementById('liveToastBtn');
// var toastLiveExample = document.getElementById('liveToast');
// if (toastTrigger) {
// 	toastTrigger.addEventListener('click', function () {
// 		var toast = new bootstrap.Toast(toastLiveExample);

// 		toast.show();
// 	});
// }
</script>

<style>
/* TODO : https://autoprefixer.github.io/ */
#app {
	align-items: center;
	padding: 50px;
}

@media only screen and (max-width: 992px) {
	#app {
		padding: 20px;
	}
}

@media only screen and (max-width: 576px) {
	#app {
		padding: 5px;
	}
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
	transform: scale(1.2);
	transition-duration: 0.25s;
	transition-timing-function: ease-out;
	z-index: 999;
}

.rounded-start-input {
	border-radius: 0 !important;
	border-start-start-radius: 0.375rem !important;
	border-end-start-radius: 0.375rem !important;
}

.rounded-end-input {
	border-radius: 0 !important;
	border-end-end-radius: 0.375rem !important;
	border-start-end-radius: 0.375rem !important;
}
</style>

<!-- npm run dev -->
