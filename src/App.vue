<template>
    <main>
        <div class="task-iput-container">
            <label for="inputText">Enter a new task</label>
            <input type="text" id="inputText" v-model="newTask" @keyup.enter="addNewTask">
        </div>
        <div class="task-list-container">
            <ul>
                <li>test</li>
                <li>test</li>
                <li>test</li>
                <li>test</li>
            </ul>
        </div>

    </main>
</template>
<!-- ---------------------------- -->
<script setup lang='ts'>
import { ref } from 'vue'

const newTask = ref("");

const addNewTask = () => {
    console.log(newTask.value);

    newTask.value = ""
}

</script>
<!-- ---------------------------- -->
<style></style>