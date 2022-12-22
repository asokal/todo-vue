<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">
				Привет,
				<input v-model="name" type="text" placeholder="Введите имя" />
			</h2>
		</section>

		<section class="create-todo">
			<h3>СОЗДАТЬ ЗАДАЧИ</h3>

			<form @submit.prevent="addTodo">
				<h4>Что нужно сделать?</h4>
				<input
					v-model="input_content"
					type="text"
					placeholder="например: сходить в магазин"
				/>

				<h4>Выберите категорию</h4>

				<div class="options">
					<label>
						<input
							v-model="input_category"
							type="radio"
							name="category"
							value="business"
						/>
						<span class="bubble business"></span>
						<div>Business</div>
					</label>

					<label>
						<input
							v-model="input_category"
							type="radio"
							name="category"
							value="personal"
						/>
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>
				</div>

				<input type="submit" value="Добавить задачу" />
			</form>
		</section>

		<section class="todo-list" v-if="todos.length">
			<h3>СПИСОК ЗАДАЧ</h3>

			<div
				v-for="todo in todos_asc"
				:key="todo.id"
				:class="`todo-item ${todo.done && 'done'}`"
			>
				<label>
					<input v-model="todo.done" type="checkbox" />
					<span :class="`bubble ${todo.category}`"></span>
				</label>

				<div class="todo-content">
					<input v-model="todo.content" type="text" />
				</div>

				<div class="actions">
					<button class="delete" @click="removeTodo(todo)">
						Удалить
					</button>
				</div>
			</div>
		</section>
	</main>
</template>

<script setup>
	import { ref, onMounted, computed, watch } from 'vue';

	// реактивные переменные
	const todos = ref([]);
	const name = ref('');

	const input_content = ref('');
	const input_category = ref('business');

	// отсортировать задачи по возрастанию(дата создания)
	const todos_asc = computed(() =>
		todos.value.sort((a, b) => {
			return b.id - a.id;
		})
	);

	// добавить задачу
	const addTodo = () => {
		if (
			input_content.value.trim() === '' ||
			input_category.value === null
		) {
			return;
		}

		todos.value.push({
			id: Date.now(),
			content: input_content.value,
			category: input_category.value,
			done: false,
		});

		input_content.value = '';
	};

	// удалить задачу
	const removeTodo = (todo) => {
		todos.value = todos.value.filter((t) => t.id !== todo.id);
	};

	// сохранить задачи
	watch(
		todos,
		(newVal) => {
			localStorage.setItem('todos', JSON.stringify(newVal));
		},
		{ deep: true }
	);

	// сохранить имя пользователя
	watch(name, (newVal) => {
		localStorage.setItem('name', newVal);
	});

	// вывести имя пользователя
	onMounted(() => {
		name.value = localStorage.getItem('name') || '';
		todos.value = JSON.parse(localStorage.getItem('todos')) || [];
	});
</script>

<style scoped>
	.app {
		max-width: 600px;
		margin: 0 auto;
	}
</style>
