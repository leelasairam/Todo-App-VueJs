<script setup>
import { ref, computed, onMounted, watch } from 'vue'

defineProps({
  msg: {
    type: String,
    required: true
  }
})

const NewTodo=ref('');
const TodoList = ref([]);
const hideCompleted = ref(false);

onMounted(() => {
  const StoredTodos = localStorage.getItem('todos');
  if(StoredTodos){
    TodoList.value = JSON.parse(StoredTodos);
  }
  console.log(typeof(TodoList.value))
});

watch(TodoList, UpdateLocalStorage, { deep: true });

const filteredTodos = computed(() => {
  return hideCompleted.value
    ? TodoList.value.filter((t) => t.completed == false)
    : TodoList.value
})

function UpdateLocalStorage(){
  localStorage.setItem("todos",JSON.stringify(TodoList.value));
}

function AddNewTodo(){
  if(NewTodo.value!==''){
    TodoList.value.push({id:TodoList.value.length,text:NewTodo.value,completed:false,created:new Date().toString().slice(0,24)});
    NewTodo.value = '';
    UpdateLocalStorage();
  }
  else{
    alert('Please enter New Todo')
  }
}

function RemoveTodo(todo){
  TodoList.value = TodoList.value.filter((t) => t !== todo);
  UpdateLocalStorage();
}

function DeleteCompletedItems(){
  TodoList.value = TodoList.value.filter((i) => i.completed==false);
  UpdateLocalStorage();
}

function scrollToBottom() {
    window.scrollTo({
      top: document.documentElement.scrollHeight,
      behavior: 'smooth',
    });
  }

</script>

<template>
  
    <h1 class="green">{{ msg }}</h1>
    <div>
      <input v-model="NewTodo" />
      <button class="btngap" @click="AddNewTodo">Add</button>
      <button class="btngap" @click="scrollToBottom">&#8595;</button>
    </div><br/>
    
    
    <div v-for="i in filteredTodos.slice().reverse()" :key="i.id">
      <div class="card card-margin">
        <p class="card__content">
          <span><input v-model="i.completed"  type="checkbox" /></span>  
          <span :class="{ strike: i.completed }">{{ i.text }}</span> 
        </p>
        <div class="card__date">
            {{i.created}}
        </div>
        <div class="card__arrow">
          <button style="background-color: brown;color: white;border: none;" class="btngap" @click="RemoveTodo(i)">X</button>
        </div>
      </div>
    </div>

    <div style="margin-top: 1rem;">
        <button class="btngap" @click="hideCompleted=!hideCompleted">{{ hideCompleted ? 'Show All' : 'Hide Completed' }}</button>
        <button class="btngap" @click="DeleteCompletedItems">Delete Completed</button>
    </div>

</template>

<style scoped>
  .card-margin{
    margin-bottom: 0.5rem;
  }
  .strike{
    text-decoration: line-through;
  }
  .btngap{
      margin-left: 0.5rem;
    }
    .card {
  --border-radius: 0.75rem;
  --primary-color: #FDF4F5;
  --secondary-color: #3c3852;
  width: 240px;
  font-family: "Arial";
  padding: 1rem;
  cursor: pointer;
  border-radius: var(--border-radius);
  background: #FDF4F5;
  box-shadow: 0px 8px 16px 0px rgb(0 0 0 / 3%);
  position: relative;
}

.card > * + * {
  margin-top: 1.1em;
}

.card .card__content {
  color: var(--secondary-color);
  font-size: 0.86rem;
}

.card .card__title {
  padding: 0;
  font-size: 1.3rem;
  font-weight: bold;
}

.card .card__date {
  color: #6e6b80;
  font-size: 0.8rem;
}

.card .card__arrow {
  position: absolute;
  background: var(--primary-color);
  padding: 0.4rem;
  border-top-left-radius: var(--border-radius);
  border-bottom-right-radius: var(--border-radius);
  bottom: 0;
  right: 0;
  transition: 0.2s;
  display: flex;
  justify-content: center;
  align-items: center;
}

.card svg {
  transition: 0.2s;
}

/* hover */
.card:hover .card__title {
  color: var(--primary-color);
  text-decoration: underline;
}

.card:hover .card__arrow svg {
  transform: translateX(3px);
}
</style>
