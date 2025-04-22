<template>
    <div class="task-list">
        <h1>Задачи:</h1>
        <div v-for="task in tasks" :key="task.id">
            <label class="task-item" :class="{ 'completed': task.done }">
                <input type="checkbox" v-model="task.done" @change="saveTasks">
                <span>
                    {{ task.title }}
                </span>
            </label>
        </div>
    </div>
</template>

<script>
export default {
    name: 'TaskList',
    data() {
        return {
            tasks: []
        }
    },
    methods: {
        async fetchTasks() {
            try {
                const response = await fetch('/tasks.json');
                const fetchedTasks = await response.json();

                const savedTasks = localStorage.getItem('tasks');

                if (savedTasks) {
                    const parsedSavedTasks = JSON.parse(savedTasks);

                    // compare fetched to saved tasks
                    const areTasksDifferent =
                        fetchedTasks.length !== parsedSavedTasks.length ||
                        fetchedTasks.some((fetchedTask, index) => {
                            const savedTask = parsedSavedTasks[index];
                            return (
                                !savedTask ||
                                fetchedTask.id !== savedTask.id ||
                                fetchedTask.title !== savedTask.title
                            );
                        });

                    if (areTasksDifferent) {
                        // use fetched if different
                        this.tasks = fetchedTasks;
                        console.log('tasks changed, using new fetched tasks');
                    } else {
                        // else use saved
                        this.tasks = parsedSavedTasks;
                    }
                } else {
                    // no saved, use fetched
                    this.tasks = fetchedTasks;
                }

                this.saveTasks();
            } catch (error) {
                console.error('Error loding tasks:', error);
            }
        },
        saveTasks() {
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        }
    },
    mounted() {
        this.fetchTasks();
    }
}
</script>

<style>
.task-list {
    margin-top: 20px;
    text-align: left;
}

.task-item {
    padding: 10px;
    border-bottom: 1px solid #eee;
    display: flex;
    align-items: center;
    cursor: pointer;
}

.task-item input {
    margin-right: 2rem;
    scale: 1.5;
}

.task-item span {
    font-size: 1.25rem;
    user-select: none;
}

.completed {
    text-decoration: line-through;
    color: #888;
}
</style>