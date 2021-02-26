<template>
	<div class="pt-5">
		<v-row class="d-flex justify-center">
			<v-col cols="6">
				<v-text-field
					label="New Todo"
					outlined
					v-model="toDo.title"
				></v-text-field>
			</v-col>
			<v-col cols="1">
				<v-btn large color="primary" :disabled="isDisabled" @click="addToDo"
					>Add</v-btn
				>
			</v-col>
		</v-row>
		<v-row class="d-flex justify-center pa-5 mx-auto">
			<v-col cols="3">
				<v-select
					v-model="select"
					:items="toDoUserId"
					:rules="[(v) => !!v || 'Item is required']"
					label="Show Todos by User ID"
					required
					@change="filterResultsByUserId(select)"
				></v-select>
			</v-col>
			<v-col cols="4">
				<v-checkbox
					v-model="status"
					:label="status ? 'Hide Completed' : 'Show Completed'"
					color="primary"
					:value="status != status"
					@change="filterResultsByCompleted(status)"
					hide-details
				></v-checkbox>
			</v-col>
		</v-row>
		<transition-group name="fade">
			<v-card
				class="mx-auto pa-0 ma-5 mx-auto text-center"
				max-width="400"
				v-for="toDo in toDos"
				:key="toDo.id"
			>
				<v-system-bar class="white accent-4 ma-0 pa-0 ">
					<h3 class="my-4 green--text mx-auto pt-4" v-if="toDo.completed">
						This is completed
					</h3>
				</v-system-bar>

				<div v-if="!toDo.isEditing">
					<v-card-title>
						{{ toDo.title }}
					</v-card-title>
				</div>
				<div v-else>
					<v-card-title>
						<v-text-field
							v-model="toDo.title"
							@keyup.enter="updateToDo(toDo)"
						></v-text-field>
						<v-btn icon @click="updateToDo(toDo)"
							><v-icon>mdi-check</v-icon></v-btn
						>
					</v-card-title>
				</div>
				<v-card-actions class="d-flex align-center justify-center">
					<v-checkbox
						v-model="toDo.completed"
						label="Completed"
						color="primary"
						:value="toDo.completed != toDo.completed"
						hide-details
					></v-checkbox>
					<v-spacer></v-spacer>
					<v-btn icon color="primary" @click="editToDo(toDo)">
						<v-icon>mdi-pencil</v-icon>
					</v-btn>
					<v-btn icon color="red" @click="deleteToDo(toDo.id)">
						<v-icon>mdi-delete</v-icon>
					</v-btn>
				</v-card-actions>
			</v-card>
		</transition-group>
		<transition-group name="fade">
			<v-card
				class="mx-auto pa-0 ma-5 mx-auto text-center"
				max-width="400"
				v-for="toDo in filteredToDos"
				:key="toDo.id"
			>
				<v-system-bar class="white accent-4 ma-0 pa-0 ">
					<h3 class="my-4 green--text mx-auto pt-4" v-if="toDo.completed">
						This is completed
					</h3>
				</v-system-bar>

				<div v-if="!toDo.isEditing">
					<v-card-title>
						{{ toDo.title }}
					</v-card-title>
				</div>
				<div v-else>
					<v-card-title>
						<v-text-field
							v-model="toDo.title"
							@keyup.enter="updateToDo(toDo)"
						></v-text-field>
						<v-btn icon @click="updateToDo(toDo)"
							><v-icon>mdi-check</v-icon></v-btn
						>
					</v-card-title>
				</div>
				<v-card-actions class="d-flex align-center justify-center">
					<v-checkbox
						v-model="toDo.completed"
						label="Completed"
						color="primary"
						:value="toDo.completed != toDo.completed"
						hide-details
					></v-checkbox>
					<v-spacer></v-spacer>
					<v-btn icon color="primary" @click="editToDo(toDo)">
						<v-icon>mdi-pencil</v-icon>
					</v-btn>
					<v-btn icon color="red" @click="deleteToDo(toDo.id)">
						<v-icon>mdi-delete</v-icon>
					</v-btn>
				</v-card-actions>
			</v-card>
		</transition-group>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				toDos: [],
				toDo: {},
				filteredToDos: [],
				toDoUserId: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
				select: null,
				status: false,
			};
		},
		mounted() {
			this.getToDos();
			this.filterResultsByUserId(null);
			this.filterResultsByCompleted(null);
		},
		computed: {
			isDisabled() {
				return this.toDo.title === undefined;
			},
		},
		methods: {
			async getToDos() {
				let uri = "https://jsonplaceholder.typicode.com/todos";
				await fetch(uri)
					.then((response) => {
						if (response.ok) {
							return response.json();
						}
					})
					.then((data) => {
						const results = [];
						for (const id in data) {
							results.push(data[id]);
						}

						this.filteredToDos = results;
					});
			},
			async addToDo() {
				let uri = "https://jsonplaceholder.typicode.com/todos";
				await fetch(uri, {
					method: "POST",
					body: JSON.stringify({
						title: this.toDo.title,
						id: Math.random() * 1,
						completed: false,
						userId: 1,
					}),
					headers: {
						"Content-type": "application/json; charset=UTF-8",
					},
				});
				this.toDos.unshift({
					title: this.toDo.title,
					id: Math.random() * 1,
					completed: false,
					userId: 1,
				});
				this.toDo = {};
			},
			deleteToDo(id) {
				fetch(`https://jsonplaceholder.typicode.com/posts/${id}`, {
					method: "DELETE",
				});
				this.filteredToDos = this.filteredToDos.filter((toDo) => {
					return toDo.id !== id;
				});
				this.toDos = this.toDos.filter((toDo) => {
					return toDo.id !== id;
				});
			},
			async filterResultsByUserId(userId) {
				let uri;
				if (userId !== null) {
					uri = `https://jsonplaceholder.typicode.com/todos?userId=${userId}&completed=false`;
				} else {
					uri = `https://jsonplaceholder.typicode.com/todos?completed=false`;
				}
				await fetch(uri)
					.then((response) => {
						if (response.ok) {
							return response.json();
						}
					})
					.then((data) => {
						const results = [];
						for (const id in data) {
							results.push(data[id]);
						}

						this.filteredToDos = results;
					});
			},
			async filterResultsByCompleted(completed) {
				let uri;
				if (completed !== null) {
					uri = `https://jsonplaceholder.typicode.com/todos?completed=${completed}`;
				} else {
					uri = `https://jsonplaceholder.typicode.com/todos?completed=false`;
				}
				await fetch(uri)
					.then((response) => {
						if (response.ok) {
							return response.json();
						}
					})
					.then((data) => {
						const results = [];
						for (const id in data) {
							results.push(data[id]);
						}

						this.filteredToDos = results;
					});
			},
			// editToDo(toDo) {
			// 	toDo.isEditing = true;
			// },
			// updateToDo(toDo) {
			// 	toDo.isEditing = false;
			// },
		},
	};
</script>

<style lang="scss" scoped></style>
