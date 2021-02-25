<template>
	<div class="pt-5">
		<v-row class="d-flex justify-center">
			<v-col cols="6">
				<v-text-field
					label="New Todo"
					outlined
					v-model="toDo.title"
					@keyup.enter="addToDo"
				></v-text-field>
			</v-col>
			<v-col cols="1">
				<v-btn large color="primary" @click="addToDo">Add</v-btn>
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
	</div>
</template>

<script>
	export default {
		data() {
			return {
				toDos: [],
				toDo: {},
			};
		},
		methods: {
			addToDo() {
				this.toDos.push({
					title: this.toDo.title,
					id: Math.random() * 1,
					completed: false,
					isEditing: false,
				});
				this.toDo = {};
			},
			deleteToDo(id) {
				this.toDos = this.toDos.filter((toDo) => {
					return toDo.id !== id;
				});
			},
			editToDo(toDo) {
				toDo.isEditing = true;
			},
			updateToDo(toDo) {
				toDo.isEditing = false;
			},
		},
	};
</script>

<style lang="scss" scoped></style>
