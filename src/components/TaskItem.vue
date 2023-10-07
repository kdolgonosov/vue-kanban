<template>
    <li class="task-item">
        <img class="task-item__img" :src="task.image" />
        <div class="task-item__text-wrapper">
            <span v-if="!this.editMode">{{ task.title }}</span>
            <input v-else v-model="this.editTaskForm.title" class="task-item__input" />
            <span class="task-item__category" v-if="!this.editMode">{{ task.category }}</span>
            <input v-else v-model="this.editTaskForm.category" class="task-item__input" />
            <span v-if="!this.editMode">&#36;{{ task.price }}</span>
            <input v-else v-model="this.editTaskForm.price" class="task-item__input" />
            <span v-if="!this.editMode" class="task-item__description">{{ task.description }}</span>
            <input v-else v-model="this.editTaskForm.description" class="task-item__input" />
            <div class="task-item__rating">
                <star-rating
                    :inline="true"
                    :star-size="20"
                    :read-only="true"
                    :rating="task.rating.rate"
                    :round-start-rating="false"
                ></star-rating>
                <span class="task-item__rating_count">({{ task.rating.count }})</span>
            </div>
        </div>
        <div class="task-item__actions">
            <button
                class="task-item__actions_button task-item__actions_button_type_delete"
                @click="this.$emit('remove', task.id, this.type)"
                v-if="!this.editMode"
            ></button>
            <button
                class="task-item__actions_button task-item__actions_button_type_save"
                @click="this.saveChanges()"
                v-else
            ></button>
            <button
                class="task-item__actions_button task-item__actions_button_type_edit"
                @click="this.toggleEditMode()"
                v-if="!this.editMode"
            ></button>
            <button
                class="task-item__actions_button task-item__actions_button_type_discard"
                @click="this.discardChanges()"
                v-else
            ></button>
            <button
                class="task-item__actions_button"
                :class="{
                    type_play: this.type === 'backlog',
                    type_finish: this.type === 'inprogress',
                }"
                @click="this.$emit('move', task.id, this.type)"
                v-if="!this.editMode"
            ></button>
        </div>
    </li>
</template>

<script>
import StarRating from 'vue-star-rating';

export default {
    name: 'TaskItem',
    components: {
        StarRating,
    },
    data() {
        return {
            editMode: false,
            editTaskForm: {
                id: this.task.id,
                title: this.task.title,
                price: this.task.price,
                description: this.task.description,
                category: this.task.category,
                image: this.task.image,
                rating: {
                    count: this.task.rating.count,
                    rate: this.task.rating.rate,
                },
            },
            // editTaskForm: { ...this.task },
        };
    },
    props: {
        task: Object,
        type: String,
    },
    emits: ['remove', 'edit', 'move'],
    methods: {
        toggleEditMode() {
            this.editMode = !this.editMode;
        },
        saveChanges() {
            this.$emit('edit', this.task.id, this.type, this.editTaskForm);
            this.toggleEditMode();
        },
        discardChanges() {
            this.editTaskForm = this.task;
            this.toggleEditMode();
        },
    },
};
</script>

<style scoped>
.task-item {
    height: 150px;
    padding: 15px;
    margin-bottom: 10px;
    display: flex;
    flex-direction: row;
    border-radius: 25px;
    background-color: #fff;
    z-index: 1;
}
.task-item:hover {
    cursor: grab;
}
.task-item_type_backlog {
    border-left: 10px solid #aac5ff;
}
.task-item_type_inprogress {
    border-left: 10px solid #ffcd88;
}
.task-item_type_done {
    border-left: 10px solid #d1ffa5;
}
.task-item__img {
    width: 100px;
    height: 100px;
    margin-right: 15px;
}
.task-item__text-wrapper {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    justify-content: space-between;
    width: 100%;
}
.task-item__input {
    width: 90%;
}
.task-item__description {
    display: block;
    max-height: 36px;
    overflow: hidden;
    text-overflow: ellipsis;
}
.task-item__category {
    border-radius: 15px;
    padding: 5px;
    background-color: #e3e3e3;
}
.task-item__rating {
    display: flex;
    align-items: center;
}
.task-item__rating_count {
    margin-left: 5px;
}
.task-item__actions {
    width: 30px;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
}
.task-item__actions_button {
    border: 0;
    padding: 0;
    background-color: transparent;
    width: 30px;
    height: 30px;
    background-size: contain;
    background-repeat: no-repeat;
}
.task-item__actions_button:hover {
    cursor: pointer;
}
.task-item__actions_button_type_delete {
    background-image: url(../assets/bucket-icon.svg);
}
.task-item__actions_button_type_edit {
    background-image: url(../assets/edit-icon.svg);
}
.type_play {
    background-image: url(../assets/play-icon.svg);
}
.type_finish {
    background-image: url(../assets/check-icon.svg);
}
.task-item__actions_button_type_save {
    background-image: url(../assets/check-icon.svg);
    margin-bottom: 10px;
}
.task-item__actions_button_type_discard {
    background-image: url(../assets/close-icon.svg);
}
</style>
