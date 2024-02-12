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
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((task) => task !== todo);
};

watch(
  todos,
  (newValue) => {
    localStorage.setItem("todos", JSON.stringify(newValue));
  },
  {
    deep: true,
  }
);

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Halo,
        <input type="text" placeholder="ketik namamu disini" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Buat catatan kamu hari ini!</h3>
      <form @submit.prevent="addTodo">
        <h4>Apa yang akan kamu kerjakan?</h4>
        <input
          type="text"
          placeholder="Saya akan belajar coding!"
          v-model="input_content"
        />
        <h4>Pilih Tempat</h4>

        <div class="options">
          <label
            ><input
              type="radio"
              name="category"
              value="kuliah"
              id="category1"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Kuliah</div>
          </label>

          <label
            ><input
              type="radio"
              name="category"
              value="rumah"
              id="category2"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Rumah</div>
          </label>
        </div>

        <input type="submit" value="Tambah Catatan" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Apa yang akan aku kerjakan sekarang :</h3>
      <div class="list">
        <div
          v-for="todo in todos_asc"
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
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
