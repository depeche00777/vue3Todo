<script>
import { ref, watch,onMounted, computed } from 'vue';

export default {
  setup () {
    const name = ref('')
    const input_content = ref('')
    const input_category =ref(null)

    const todos = ref([]) //투두, 카테고리

    watch(name, (newVal)=>{
      localStorage.setItem('name', newVal);
    })

    //입력값을 받아서 배열에 넣는 함수
    const addTodo = () =>{
      if(input_content.value.trim() === '' || input_category.value === null){
        return
      }
      todos.value.push({
        content: input_content.value,
        category: input_category.value,
        createAt: new Date().getTime(),   //최신을 위에 올려놓기위해
        done: false  
      })
      //입력 후 초기화
      input_content.value = '';
      input_category.value = null
    }

    //localStorage에 입력값 등록
    watch(todos, (newTodo) => {
      console.log('todos 배열 바뀜!!');
      localStorage.setItem('todos', JSON.stringify(newTodo));
    },{deep:true})

    // 시간순으로 정리(최신이 위에 오게)
    const todos_asc = computed(() => todos.value.sort((a,b) => {
        return b.createAt-a.createAt;
      })
    )


    //새로 렌더링 될때 localStorage 불러오기
    onMounted(()=>{
      name.value = localStorage.getItem('name') || '';
      todos.value = JSON.parse(localStorage.getItem('todos') || [])
    })

    //삭제 함수
    const removeTodo = (todo) => {
      todos.value= todos.value.filter((item)=> item !== todo )
    }
    
    return{  
      name, input_content, input_category, addTodo, todos, removeTodo, todos_asc
    }
  }
} 
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
        What's up 
        <input type="text" placeholder="name here" v-model="name">
      </h1>
    </section>

    <section class="create_todo">
      <h2>CREATE A TODO</h2>
      <form id="" v-on:submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          class="todo_input"
          type="text" 
          id="content" 
          placeholder="할일을 입력해주세요"
          v-model="input_content"> 
        {{ input_content }} 

        <div class="options">
          <label class="label">
            <input type="radio" name="catagory" id="catagory1" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label class="label">
            <input type="radio" name="catagory" id="catagory1" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label> 
          선택한 카테고리 - {{ input_category  }}      
        </div>
        <!-- options 선택 끝 -->
        <input type="submit" value="Add TODO">
      </form>
    </section>

    <section class="todo_list">
      <h3>TODO LIST</h3>
      <div id="todo_list" class="list">
        <div 
          :class="`todo_item ${todo.done && 'done'}`" 
          v-for="todo in todos_asc">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span v-bind:class ="`bubble ${todo.category}`" ></span>
          </label>
          <div class="todo_content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style lang="scss" scoped>

</style>
