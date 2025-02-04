<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const tasks = ref([]);
const newTask = ref({
    title: '',
    description: '',
});

const fetchTasks = async () => {
    try {
        const response = await axios.get('/api/tasks');
        tasks.value = response.data;
    } catch (error) {
        console.error('An error occurred while importing tasks:', error);
    }
};

const createTask = async () => {
    try {
        const response = await axios.post('/api/tasks', newTask.value);
        tasks.value.push(response.data);
        newTask.value.title = '';
        newTask.value.description = '';
    } catch (error) {
        console.error('An error occurred while adding the task:', error);
    }
};

const deleteTask = async (taskId) => {
    try {
        await axios.delete(`/api/tasks/${taskId}`);
        tasks.value = tasks.value.filter((task) => task.id !== taskId);
    } catch (error) {
        console.error('An error occurred while deleting the task:', error);
    }
};

const updateTask = async (taskId, taskStatus) => {
    try {
        const response = await axios.put(`/api/tasks/${taskId}`, {
            status: taskStatus === 'Pending' ? 'Completed' : 'Pending',
        });
        tasks.value = tasks.value.map((task) => {
            if (task.id === taskId) {
                return response.data;
            }
            return task;
        });
    } catch (error) {
        console.error('An error occurred while updating the task:', error);
    }
};

onMounted(fetchTasks);
</script>

<template>

    <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
        <div class="p-6 ">
            <div>
                <h2 class="text-xl font-semibold leading-tight text-gray-800">Add New Task</h2>
                <div class="mt-4">
                    <div class="">
                        <label for="newTask.title" class="block text-sm/6 font-medium text-gray-900">Title</label>
                        <div class="mt-2">
                            <input
                                v-model="newTask.title"
                                type="text"
                                id="newTask.title"
                                class="block w-full rounded-md bg-white px-3 py-1.5 text-base text-gray-900 outline-1 -outline-offset-1 outline-gray-300 placeholder:text-gray-400 focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600 sm:text-sm/6"
                            />
                        </div>
                    </div>
                    <div class="mt-3">
                        <label for="newTask.description" class="block text-sm/6 font-medium text-gray-900">Description</label>
                        <div class="mt-2">
                    <textarea
                        v-model="newTask.description"
                        placeholder=""
                        class="block w-full rounded-md bg-white px-3 py-1.5 text-base text-gray-900 outline-1 -outline-offset-1 outline-gray-300 placeholder:text-gray-400 focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600 sm:text-sm/6"
                    ></textarea>
                        </div>
                    </div>
                    <button @click="createTask" class="mt-5 rounded-md bg-indigo-600 px-3 py-2 text-sm font-semibold text-white shadow-xs hover:bg-indigo-500 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">Add Task</button>
                </div>
            </div>
        </div>
    </div>

    <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg mt-5">
        <div class="p-6 ">

            <h2 class="text-xl font-semibold leading-tight text-gray-800">Task List</h2>


            <ol class="mt-3 grid grid-cols-1 gap-8 lg:grid-cols-2">
                <li class="flex" v-for="task in tasks" :key="task.id">
                    <div class="">
                        <h2 class="flex items-center text-sm/6 font-semibold">
                            <span class="text-indigo-500">Task {{ task.id }}</span>
                            <span class="ml-2 h-4 w-px bg-slate-300"></span>
                            <span class="ml-2 text-slate-900">
                                <span class="xl:hidden">{{ task.title }}</span>
                                <span class="hidden xl:inline">{{ task.title }}</span>
                            </span>
                            <span class="ml-2 h-4 w-px bg-slate-300"></span>
                            <span class="ml-2 text-slate-900">
                                <span class="xl:hidden">{{ task.status }}</span>
                                <span class="hidden xl:inline">{{ task.status }}</span>
                            </span>
                            <span class="ml-2 h-4 w-px bg-slate-300"></span>
                            <button @click="updateTask(task.id, task.status)" class=" pointer-events-auto ml-2 rounded-md bg-blue-600 px-3 py-2 text-[0.8125rem]/5 font-semibold text-white hover:bg-blue-500">Update</button>
                            <span class="ml-2 h-4 w-px bg-slate-300"></span>
                            <button @click="deleteTask(task.id)" class=" pointer-events-auto ml-2 rounded-md bg-indigo-600 px-3 py-2 text-[0.8125rem]/5 font-semibold text-white hover:bg-indigo-500">Delete</button>
                        </h2>
                        <p class="mt-2 text-sm/7 text-slate-600">{{ task.description }}</p>
                    </div>
                </li>
            </ol>

        </div>
    </div>


</template>
