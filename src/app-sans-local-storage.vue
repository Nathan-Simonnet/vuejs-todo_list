<template>
    <main>
        <div class="task-input-container">
            <label for="inputText">Enter a new task</label>
            <input type="text" id="inputText" v-model="newTask" @keyup.enter="addNewTask" placeholder="Walk the dog">
        </div>
        <div class="task-list-container">
            <ul>
                <li v-for="(task, index) in taskList" :key="'task-' + index" :class="task.state"
                    @click="taskHandler(task)">{{ task.taskName }}</li>
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
    state: string;
    id: number;
}

const newTask = ref("");
const taskPlaceholder = ref(false);

const taskList = ref<Task[]>([
    {
        taskName: "Groceries",
        state: "incompleted",
        id: Date.now()
    },
    {
        taskName: "Dentist",
        state: "incompleted",
        id: Date.now() + 1
    }, {
        taskName: "Read",
        state: "completed",
        id: Date.now() + 2
    }
]);

const addNewTask = () => {

    if (newTask.value == "") { return };

    // If placeholder, nothing else is in the array, so we delete the first (and only) element placeholder before adding the new task
    if(taskPlaceholder.value){
        taskPlaceholder.value = false;
        taskList.value.pop();
    };

    taskList.value.push({
        taskName: newTask.value,
        state: "incompleted",
        id: Date.now(),
    });
    newTask.value = "";
};

// Define the taskHandler function
function taskHandler(task: Task) {
    if (task.state === "incompleted") {
        task.state = "completed";
    } else if (task.state === "completed") {
        taskList.value = taskList.value.filter((taskToKeep) => taskToKeep.id !== task.id)
    };

    if (taskList.value.length === 0) {
        console.log('ha');
        taskList.value.push({
            taskName: "Nothing for now, congratulation !",
            state: "placeholder",
            id: Date.now(),
        });
        taskPlaceholder.value = true;
    }
}

</script>
<!-- ---------------------------- -->
<style></style>
