<template>
	<v-app>
		<v-app-bar app color="deep-purple" dark>
			<div class="d-flex align-center">
				<h2>ToDo App</h2>
			</div>

			<v-spacer></v-spacer>
		</v-app-bar>

		<v-main>
			<to-do-list />
		</v-main>
	</v-app>
</template>

<script>
	import ToDoList from "@/components/ToDoList.vue";
	export default {
		name: "App",

		components: {
			ToDoList,
		},

		data: () => ({
			//
		}),
	};
</script>
