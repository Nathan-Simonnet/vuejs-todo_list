<!-- IDEAS
    click droit pour annuler l'action de checkmarkage
    -->

<template>
    <main>
        <div class="task-input-container">
            <label for="inputText">Enter a new task</label>
            <input type="text" id="inputText" v-model="newTask" @keyup.enter="addNewTask" placeholder="Walk the dog">
            <button @click="addNewTask">Add Task</button>
        </div>
        <div class="task-list-container">
            <ul>
                <li v-for="task in taskList" :key="task.id" :id="task.id" :class="task.state"
                    @click="taskHandler(task)">
                    {{ task.taskName }}</li>
            </ul>
            <button :class="{ 'hidden': taskPlaceholder }" @click="showTaskModal" @keyup.enter="clearAllTasks">Clear all
                tasks</button>
        </div>

        <div id="task-modal" :class="{'hidden': !taskModalIsVisible}">
            <h3>Are you sure you want to delete all task?</h3>
            <div>
                <button  @click="() => { hideTaskModal(); clearAllTasks(); }" >YES</button>
                <button @click="hideTaskModal" >NO!</button>
            </div>
        </div>
    </main>
</template>
<!-- ---------------------------- -->
<script setup lang='ts'>
import { ref, onMounted } from 'vue'

interface Task {
    taskName: string;
    state: string;
    id: string;
}

const taskList = ref<Task[]>([
    {
        taskName: "Groceries",
        state: "incompleted",
        id: crypto.randomUUID()
    },
    {
        taskName: "Dentist",
        state: "incompleted",
        id: crypto.randomUUID()
    }, {
        taskName: "Read",
        state: "completed",
        id: crypto.randomUUID()
    }
]);

const newTask = ref("");
// Do we have to add a placholder or not?
const taskPlaceholder = ref(false);

const addNewTask = () => {
    if (newTask.value.trim() === "") { return };

    // If placeholder, nothing else is in the array, so we delete the first (and only) element placeholder before adding the new task
    if (taskPlaceholder.value) {
        taskPlaceholder.value = false;
        taskList.value.pop();
    };

    taskList.value.push({
        taskName: newTask.value.trim(),
        state: "incompleted",
        id: crypto.randomUUID(),
    });
    localStoring();
    newTask.value = "";
};

function taskHandler(task: Task) {
    if (task.state === "incompleted") {
        task.state = "completed";
    } else if (task.state === "completed") {
        taskList.value = taskList.value.filter((taskToKeep) => taskToKeep.id !== task.id)
    };
    // Add placeholder if empty
    if (taskList.value.length === 0) {
        taskList.value.push({
            taskName: "Nothing for now !",
            state: "placeholder",
            id: crypto.randomUUID(),
        });
        taskPlaceholder.value = true;
    };
    localStoring();
};

//store taskList.value from local storage 
//store placeholder state from local storage 
const localStoring = () => {
    localStorage.setItem('TASK_LIST_KEY', JSON.stringify(taskList.value));
    localStorage.setItem('PLACEHOLDER_STATE_KEY', taskPlaceholder.value.toString());
};

//Restore taskList.value from local storage 
//Restore placeholder state from local storage 
const restoreLocalStorage = () => {
    try {
        const storedTaskList = localStorage.getItem('TASK_LIST_KEY');
        const storedPlaceholderState = localStorage.getItem('PLACEHOLDER_STATE_KEY');

        if (storedTaskList) {
            taskList.value = JSON.parse(storedTaskList);
        }
        if (storedPlaceholderState) {
            taskPlaceholder.value = storedPlaceholderState === 'true';
        }
    } catch (error) {
        console.error('Error restoring localStorage:', error);
    }
};

const taskModalIsVisible= ref(false)

const showTaskModal = () => {
    taskModalIsVisible.value = true;
};

const hideTaskModal = () => {
    taskModalIsVisible.value = false;
};

const clearAllTasks = () => {
    taskList.value = []

    console.log(taskList.value)

    taskList.value.push({
        taskName: "Nothing for now !",
        state: "placeholder",
        id: crypto.randomUUID(),
    });
    taskPlaceholder.value = true;
    localStoring();
};

// Every refresh it trigger the restoration... Can be improve but for now it work
onMounted(() => {
    restoreLocalStorage();
});

</script>