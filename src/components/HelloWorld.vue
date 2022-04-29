<script setup>
	import { ref } from 'vue'
	
	defineProps({
	msg: String
	})

	const count = ref(0)

// TODO : how to do functions?
	// function reset(){
	// 	count = 0
	// };

function log(){
  console.log("abou");
}
</script>

<template>
	<h1>{{ msg }}</h1>

	<div v-if="count < 3">
		<button type="button" @click="count++">
			count is: {{ count }}
		</button>
	</div>
	
	<button type="button" @click="count = 0">
			Reset counter
	</button>

	<button type="button" @click="console.log('ads')">
			log
	</button>

</template>

<style scoped>
	a {
	color: #42b983;
	}
</style>
