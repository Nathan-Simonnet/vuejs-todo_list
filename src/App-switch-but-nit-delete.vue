


<template>
    <main>
        <div class="task-iput-container">
            <label for="inputText">Enter a new task</label>
            <input type="text" id="inputText" v-model="newTask" @keyup.enter="addNewTask">
        </div>
        <div class="task-list-container">
            <ul>
                <li  v-for="(task,index) in taskList" :key="'task-'+index" :class="{'completed' : task.isCompleted}"  @click="taskHandler(task)">{{task.taskName}}</li>
            </ul>
        </div>
    </main>
</template>
<!-- ---------------------------- -->
<script setup lang='ts'>
import { ref } from 'vue'

// Define the Task interface
interface Task {
  taskName: string;
  isCompleted: boolean;
  id: number;
}

const newTask = ref("");

const addNewTask = () => {
    if(newTask.value == ""){ return }

    console.log(newTask.value);
    newTask.value = "";
};

const taskList = ref<Task[]>([
    {
        taskName: "Groceries",
        isCompleted: false,
        id: Date.now(),
    },
    {
        taskName: "Dentist",
        isCompleted: false,
        id: Date.now()
    },   {
        taskName: "Read",
        isCompleted: true,
        id: Date.now()
    }
]);

// Define the taskHandler function
function taskHandler(task: Task) {
  task.isCompleted = !task.isCompleted;
}

</script>
<!-- ---------------------------- -->
<style></style>