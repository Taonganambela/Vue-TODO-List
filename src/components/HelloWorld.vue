<script setup>
import { ref, onMounted, computed, watch } from "vue";

// My functions
const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);
// const input_category2 = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <div class="text-center">
    <section class="flex justify-center center-items">
      <h2 class="text-2xl font-bold mt-4 mb-6 text center">
        <input
          type="text"
          id="name"
          placeholder="Name here"
          v-model="name"
          class="font-bold text-2xl w-[150px] border-none"
        />
      </h2>
    </section>

    <section class="create-todo">
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4 class="font-bold">Choose a category</h4>

        <div class="flex center-items justify-center mt-3 mb-3">
          <div class="mr-2 flex">
            <h1>Business</h1>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
              class="mr-2 space-x-2"
            />
            <span class="bubble business"></span>
          </div>
          <div class="ml-2 flex">
            <h1>Personal</h1>

            <input
              type="radio"
              name="category"
              id="category"
              value="personal"
              v-model="input_category"
              class="mr-2 space-x-2"
            />
            <span class="bubble personal"></span>
          </div>
        </div>

        <div class="flex justify-center">
          <input
            type="text"
            name="content"
            id="content"
            placeholder="Enter your task"
            v-model="input_content"
            class="border-2 outline-green-600 mr-4 h-12 w-[350px] border-green-600 focus:border-green-600 rounded-md"
          />

          <input
            type="submit"
            value="Add"
            class="bg-green-600 h-12 w-[50px] p-4 text-white rounded-md"
          />
        </div>
      </form>
    </section>

    <section class="todo-list">
      <h3 class="font-bold mt-3 mb-3">Lst Items</h3>

      <div class="" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <div class="flex text-center center-items justify-center">
            <label>
              <input
                type="checkbox"
                v-model="todo.done"
                class="mt-5 mr-2 outline-green-600 focus:border-green-600"
              />
              <span
                :class="`bubble ${
                  todo.category == 'business' ? 'business' : 'personal'
                }`"
              ></span>
            </label>
            <div class="mb-3">
              <input
                type="text"
                v-model="todo.content"
                :style="{
                  'text-decoration': todo.done ? 'line-through' : 'none',
                }"
                class="border-2 mr-4 h-12 w-[300px] bg-white border-white"
              />
            </div>
            <div class="">
              <button
                class="bg-green-600 text-white rounded-md h-10 p-2"
                @click="removeTodo(todo)"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>
