 <template>
  <div class="app">
    <h1>Vue任务管理器</h1>
    
    <!-- 任务输入 -->
    <div class="input-group">
      <input 
        v-model="newTask" 
        @keyup.enter="addTask"
        placeholder="输入任务按回车添加"
      >
      <button @click="addTask">添加</button>
    </div>

    <!-- 任务列表 -->
    <ul v-if="tasks.length > 0">
      <li v-for="(task, index) in tasks" :key="index">
        <input 
          type="checkbox" 
          v-model="task.completed"
        >
        <span :class="{ completed: task.completed }">
          {{ task.text }}
        </span>
        <button @click="removeTask(index)">删除</button>
      </li>
    </ul>
    <p v-else>暂无任务，请添加新任务！</p >

    <!-- 状态统计 -->
    <div class="stats">
      总计: {{ totalTasks }} | 
      已完成: {{ completedTasks }}
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// 任务数据
const newTask = ref('')
const tasks = ref([])

// 添加任务
const addTask = () => {
  if (newTask.value.trim()) {
    tasks.value.push({
      text: newTask.value.trim(),
      completed: false
    })
    newTask.value = ''
    saveTasks()
  }
}

// 删除任务
const removeTask = (index) => {
  tasks.value.splice(index, 1)
  saveTasks()
}

// 计算属性
const totalTasks = computed(() => tasks.value.length)
const completedTasks = computed(() => 
  tasks.value.filter(t => t.completed).length
)

// 本地存储功能
const saveTasks = () => {
  localStorage.setItem('vue-tasks', JSON.stringify(tasks.value))
}

onMounted(() => {
  const saved = localStorage.getItem('vue-tasks')
  if (saved) tasks.value = JSON.parse(saved)
})
</script>

<style scoped>
.app {
  max-width: 500px;
  margin: 20px auto;
  padding: 20px;
  font-family: sans-serif;
}

.input-group {
  display: flex;
  margin-bottom: 20px;
}

input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
}

button {
  padding: 10px 15px;
  background: #42b983;
  color: white;
  border: none;
  cursor: pointer;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  display: flex;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #eee;
}

.completed {
  text-decoration: line-through;
  color: #888;
}

.stats {
  margin-top: 20px;
  font-weight: bold;
}
</style>