<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main
    class="flex max-w-screen-lg justify-center flex-col mx-auto bg-slate-50 min-h-screen text-indigo-900"
  >
    <section class="section">
      <h2 class="flex font-bold text-2xl">
        What's up,
        <input
          class="ml-2 flex-1 min-w-0 font-bold text-2xl bg-slate-50 outline-none"
          type="text"
          placeholder="Name here"
          v-model="name"
        />
      </h2>
    </section>

    <section class="section">
      <h3 class="h3">CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4 class="h4 text-zinc-400">What's on your todo list ?</h4>
        <input
          class="input outline-none mb-6"
          type="text"
          placeholder="e.g. make a video"
          v-model="input_content"
        />

        <h4 class="h4">Pick a category</h4>

        <div class="grid mb-6 grid-cols-2 gap-4">
          <label class="category">
            <input
              class="hidden sr-only peer"
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />

            <span class="bubble border-blue-500 after:bg-blue-500"></span>
            <div class="text-lg mt-4">Business</div>
          </label>

          <label class="category">
            <input
              class="hidden sr-only peer"
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble border-pink-500 after:bg-pink-500"></span>
            <div class="text-lg mt-4">Personal</div>
          </label>
        </div>

        <input class="input btn" type="submit" value="Add todo" />
      </form>
    </section>

    <section class="section">
      <h3 class="h3">TODO LIST</h3>

      <div class="my-4 mx-0">
        <div
          v-for="todo in todos_asc"
          :key="todo.id"
          :class="`flex items-center bg-white px-4 py-4 rounded-lg shadow mb-4  ${
            todo.done && 'done'
          }`"
        >
          <label class="block mr-4">
            <input class="hidden" type="checkbox" v-model="todo.done" />
            <span
              :class="`flex items-center justify-center w-5 h-5 rounded-full border-solid border-2 shadow-md  ${
                todo.category == 'business'
                  ? 'border-blue-500'
                  : 'border-pink-500'
              }`"
            ></span>
          </label>
          <div class="flex-1">
            <input
              class="text-lg outline-none"
              type="text"
              v-model="todo.content"
            />
          </div>

          <div class="flex items-center">
            <button
              class="block px-2 py-2 rounded btn text-white bg-red-500"
              @click="removeTodo(todo)"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
