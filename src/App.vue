<template>
    <button class="add-new-btn" @click="handleOpenModal">Создать новый продукт</button>
    <div class="board">
        <AddFormModal v-if="addModalOpened" @add="addTask" @close="handleOpenModal" />
        <div class="column-wrapper column-wrapper_type_backlog">
            <div class="column-header">
                <span class="column-title"
                    >Необработанные
                    <span class="column-title_count">{{ this.backlogList.length }}</span></span
                >
                <button
                    class="column-sort-btn"
                    :class="{ asc: this.backlogSort === 'asc', desc: this.backlogSort === 'desc' }"
                    @click="
                        () => {
                            this.backlogSort
                                ? this.backlogSort === 'asc'
                                    ? (this.backlogSort = 'desc')
                                    : (this.backlogSort = 'asc')
                                : (this.backlogSort = 'asc');
                            this.backlogList = sortList(this.backlogList, this.backlogSort);
                        }
                    "
                ></button>
            </div>
            <VueDraggableNext tag="ul" v-model="backlogList" group="tasks" class="task-column">
                <TaskItem
                    v-for="task in backlogList"
                    @move="moveTask"
                    @remove="removeTask"
                    @edit="editTask"
                    :key="task.id"
                    :task="task"
                    :class="'task-item_type_backlog'"
                    :type="'backlog'"
                />
            </VueDraggableNext>
        </div>
        <div class="column-wrapper column-wrapper_type_inprogress">
            <div class="column-header">
                <span class="column-title"
                    >В работе<span class="column-title_count">{{
                        this.inProgressList.length
                    }}</span></span
                >
                <button
                    class="column-sort-btn"
                    :class="{
                        asc: this.inProgressSort === 'asc',
                        desc: this.inProgressSort === 'desc',
                    }"
                    @click="
                        () => {
                            this.inProgressSort
                                ? this.inProgressSort === 'asc'
                                    ? (this.inProgressSort = 'desc')
                                    : (this.inProgressSort = 'asc')
                                : (this.inProgressSort = 'asc');
                            this.inProgressList = sortList(
                                this.inProgressList,
                                this.inProgressSort,
                            );
                        }
                    "
                ></button>
            </div>
            <VueDraggableNext tag="ul" v-model="inProgressList" group="tasks" class="task-column">
                <TaskItem
                    v-for="task in inProgressList"
                    @move="moveTask"
                    @remove="removeTask"
                    @edit="editTask"
                    :key="task.id"
                    :task="task"
                    :class="'task-item_type_inprogress'"
                    :type="'inprogress'"
                />
            </VueDraggableNext>
        </div>
        <div class="column-wrapper column-wrapper_type_done">
            <div class="column-header">
                <span class="column-title"
                    >Выполненные<span class="column-title_count">{{
                        this.doneList.length
                    }}</span></span
                >
                <button
                    class="column-sort-btn"
                    :class="{
                        asc: this.doneListSort === 'asc',
                        desc: this.doneListSort === 'desc',
                    }"
                    @click="
                        () => {
                            this.doneListSort
                                ? this.doneListSort === 'asc'
                                    ? (this.doneListSort = 'desc')
                                    : (this.doneListSort = 'asc')
                                : (this.doneListSort = 'asc');
                            this.doneList = sortList(this.doneList, this.doneListSort);
                        }
                    "
                ></button>
            </div>
            <VueDraggableNext v-model="doneList" group="tasks" class="task-column">
                <TaskItem
                    v-for="task in doneList"
                    @move="moveTask"
                    @remove="removeTask"
                    @edit="editTask"
                    :key="task.id"
                    :task="task"
                    :class="'task-item_type_done'"
                    :type="'done'"
                />
            </VueDraggableNext>
        </div>
    </div>
</template>

<script>
import { VueDraggableNext } from 'vue-draggable-next';
import TaskItem from './components/TaskItem.vue';
import AddFormModal from './components/AddFormModal.vue';
export default {
    name: 'App',
    components: {
        TaskItem,
        AddFormModal,
        VueDraggableNext,
    },
    data() {
        return {
            inputValue: '',
            backlogList: [],
            backlogSort: null,
            inProgressList: [],
            inProgressSort: null,
            doneList: [],
            doneListSort: null,
            addModalOpened: false,
        };
    },
    mounted() {
        this.getData();
    },
    methods: {
        getData() {
            fetch('https://fakestoreapi.com/products')
                .then((res) => res.json())
                .then((res) => (this.backlogList = res));
        },
        addTask(newTask) {
            this.backlogList.unshift(newTask);
            this.handleOpenModal();
        },
        removeTask(id, type) {
            if (type === 'backlog') {
                this.backlogList = this.backlogList.filter((item) => item.id !== id);
            } else if (type === 'inprogress') {
                this.inProgressList = this.inProgressList.filter((item) => item.id !== id);
            } else {
                this.doneList = this.doneList.filter((item) => item.id !== id);
            }
        },
        editTask(id, type, newTask) {
            if (type === 'backlog') {
                this.backlogList.splice(
                    this.backlogList.findIndex((item) => item.id === id),
                    1,
                    { ...newTask },
                );
            } else if (type === 'inprogress') {
                this.inProgressList.splice(
                    this.inProgressList.findIndex((item) => item.id === id),
                    1,
                    { ...newTask },
                );
            } else {
                this.doneList.splice(
                    this.doneList.findIndex((item) => item.id === id),
                    1,
                    { ...newTask },
                );
            }
        },
        moveTask(id, type) {
            if (type === 'backlog') {
                this.inProgressList.push(this.backlogList.find((el) => el.id === id));
            } else {
                this.doneList.push(this.inProgressList.find((el) => el.id === id));
            }
            this.removeTask(id, type);
        },
        sortList(arr, order) {
            return [...arr].sort((a, b) => {
                if (order === 'asc') {
                    return a.rating.rate > b.rating.rate
                        ? -1
                        : a.rating.rate < b.rating.rate
                        ? 1
                        : 0;
                } else {
                    return a.rating.rate < b.rating.rate
                        ? -1
                        : a.rating.rate > b.rating.rate
                        ? 1
                        : 0;
                }
            });
        },
        handleOpenModal() {
            this.addModalOpened = !this.addModalOpened;
        },
    },
};
</script>

<style scoped>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
:global(body) {
    margin: 0;
}
.add-new-btn {
    height: 35px;
}
.board {
    display: flex;
    flex-direction: row;
}
.column-wrapper {
    display: flex;
    flex-direction: column;
    width: calc((100vw - 50px) / 3);
    border-radius: 25px;
    height: fit-content;
    margin-right: 25px;
    padding: 0 15px;
}
.column-wrapper:last-of-type {
    margin-right: 0;
}
.column-wrapper_type_backlog {
    background-color: #7d96cd;
}
.column-wrapper_type_inprogress {
    background-color: #f5ad48;
}
.column-wrapper_type_done {
    background-color: #abe574;
}
.column-header {
    display: flex;
    flex-direction: row;
    justify-content: center;
    margin: 20px 0;
}
.column-title {
    text-align: center;
    font-size: 24px;
    font-weight: 600;
}
.column-title_count {
    padding: 5px;
    border-radius: 15px;
}
.column-sort-btn {
    border: 0;
    padding: 0;
    height: 30px;
    width: 30px;
    background-size: contain;
    background-repeat: no-repeat;
    background-image: url(./assets/sort-desc-icon.svg);
    background-color: transparent;
}
.column-sort-btn:hover {
    cursor: pointer;
}
.asc {
    background-color: rgba(255, 255, 255, 0.2);
    border-radius: 5px;
    background-image: url(./assets/sort-asc-icon.svg);
}
.desc {
    background-color: rgba(255, 255, 255, 0.2);
    border-radius: 5px;
    background-image: url(./assets/sort-desc-icon.svg);
}
.task-column {
    list-style: none;
    margin: 0;
    padding: 0;
    min-height: 100vh;
}
</style>
