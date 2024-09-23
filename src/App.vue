<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const inputContent = ref("");
const inputCategory = ref(null);

const todosCategory = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
  },
  { deep: true }
);

const addTodo = () => {
  if (inputContent.value.trim() === "" || inputCategory.value === null) {
    return;
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createAt: new Date().getTime(),
  });

  inputContent.value = "";
  inputCategory.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
}

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Как дела, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Создайте задачу</h3>

      <form @submit.prevent="">
        <h4>ЧТО В ТВОЕМ СПИСКЕ ДЕЛ?</h4>
        <input
          type="text"
          placeholder="Что нужно сделать?"
          v-model="inputContent"
        />

        <h4>Выбери категорию</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              id="category1"
              v-model="inputCategory"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              id="personal1"
              v-model="inputCategory"
            />
            <span class="bubble business"></span>
            <div>Personal</div>
          </label>
        </div>

        <input @click="addTodo()" type="submit" value="Добавить задачу" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Список задач</h3>
      <div class="list">
        <div
          v-for="todo in todosCategory"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button
              class="delete"
              @click="removeTodo(todo)"
            >
              Удалить
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
