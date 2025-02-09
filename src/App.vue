<template>
    <h1>To-Do List</h1>
    <main>
        <div class="task-input-container">
            <label for="inputText" class="sr-only">Enter a new task</label>
            <!-- Style: Better idea, use a textarea (for Germans lol), but interesting training
                    Still, it track the value.lenght of the input.value, and icrease it if > 24chaactère (and stop at 52, because....)

                    v-model: No more addEventListener -> e.target.value... Track store the value using ref() 

                    keyup/click: to trigger the functionaddNewTask
            -->
            <input :style="{ 'min-width': adjustMinWidth + 'ch' }" type="text" id="inputText" v-model="newTask"
                @keyup.enter="addNewTask" placeholder="Enter a new task">
            <button @click="addNewTask" @keyup.enter="addNewTask">Add Task</button>
        </div>
        <div class="task-list-container">
            <ul>
                <!-- For every element in taskList, create a li -->
                <li v-for="(task, index) in taskList" :key="task.id" :class="task.state"
                    @click="taskHandler(task)" @keyup.enter="taskHandler(task)" tabindex="0">{{ task.taskName }}</li>
            </ul>
            <button :class="{ 'hidden': taskPlaceholder }" @click="clearAllTasks" @keyup.enter="clearAllTasks">Clear all
                tasks</button>
        </div>
    </main>
</template>

<script setup lang='ts'>
import { ref, computed, onMounted, watch } from 'vue'

// Define the Task interface
interface Task {
    taskName: string;
    state: string;
    id: string;
}

const newTask = ref("");
const taskPlaceholder = ref(false);
const taskList = ref<Task[]>([]);

const LOCAL_STORAGE_KEY = "todoList";
const TASK_PLACEHOLDER_KEY = "taskPlaceholder";

// Function to save tasks to local storage
const saveTasks = () => {
    localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(taskList.value));
    localStorage.setItem(TASK_PLACEHOLDER_KEY, JSON.stringify(taskPlaceholder.value));
};

// Load tasks from local storage ON MOUNT
onMounted(() => {
    const savedTasks = localStorage.getItem(LOCAL_STORAGE_KEY);
    taskPlaceholder.value = (localStorage.getItem(TASK_PLACEHOLDER_KEY) === "true" ? true : false);

    if (savedTasks) {
        taskList.value = JSON.parse(savedTasks);
    } else {
        // Default tasks if no saved tasks
        taskList.value = [
            { taskName: "Groceries", state: "incompleted", id: crypto.randomUUID() },
            { taskName: "Dentist", state: "incompleted", id: crypto.randomUUID() + 1 },
            { taskName: "Read", state: "completed", id: crypto.randomUUID() + 2 }
        ];
    }
});

// Watch for changes and save tasks automatically
watch(taskList, saveTasks, { deep: true });

const addNewTask = () => {
    if (newTask.value.trim() === "") return;

    if (taskPlaceholder.value) {
        taskList.value.pop();
        taskPlaceholder.value = false;
    };

    taskList.value.push({
        taskName: newTask.value,
        state: "incompleted",
        id: crypto.randomUUID(),
    });

    newTask.value = "";
};

// Function to toggle task state or delete completed tasks
const taskHandler = (task: Task) => {
    if (task.state === "incompleted") {
        task.state = "completed";
    } else if (task.state === "completed") {
        taskList.value = taskList.value.filter(t => t.id !== task.id);
    }

    if (taskList.value.length === 0) {
        taskList.value.push({
            taskName: "Nothing for now, good job !!",
            state: "placeholder",
            id: crypto.randomUUID(),
        });
        taskPlaceholder.value = true;
    }
};

const clearAllTasks = () => {
    taskList.value = []

    console.log(taskList.value)

    taskList.value.push({
        taskName: "Nothing for now, good job !!",
        state: "placeholder",
        id: crypto.randomUUID(),
    });
    taskPlaceholder.value = true;
};

// Input With
// ===========================

// COMPUTED: here it is...
// computed properties are used to derive and return values based on reactive data (in an efficient way).
// const firstName = ref("John");
// const lastName = ref("Doe");

// const fullName = computed(() => {
//   return `${firstName.value} ${lastName.value}`;
// });

const adjustMinWidth = computed(() => {
    if (newTask.value.length > 24 && newTask.value.length < 32) {
        return newTask.value.length
    } else if (newTask.value.length >= 32) {
        return 32
    } else {
        return 24
    }
});

</script>


<style></style>