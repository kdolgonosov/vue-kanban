<template>
    <div
        class="modal"
        @click="
            () => {
                this.$emit('close');
            }
        "
    >
        <div class="modal-container" @click="(e) => e.stopPropagation()">
            <form class="modal__form" action="">
                <label>Title</label>
                <input class="modal__form_input" v-model="addTaskForm.title" type="text" />
                <label>Price</label>
                <input v-model="addTaskForm.price" type="text" />
                <label>Description</label>
                <input v-model="addTaskForm.description" type="text" />
                <label>Category</label>
                <input v-model="addTaskForm.category" type="text" />
                <label>Image url</label>
                <input v-model="addTaskForm.image" type="text" />
                <label>Rates count</label>
                <input v-model="addTaskForm.rating.count" type="text" />
                <label>Rate</label>
                <input v-model="addTaskForm.rating.rate" type="text" />
                <button
                    class="modal__form_submit-btn"
                    type="submit"
                    @click="
                        (e) => {
                            e.stopPropagation();
                            e.preventDefault();
                            this.$emit('add', addTaskForm);
                        }
                    "
                    :disabled="isFormValid"
                >
                    Создать
                </button>
            </form>
        </div>
    </div>
</template>

<script>
// import { toRaw } from 'vue';
export default {
    name: 'AddFormModal',
    // components: {
    //     StarRating,
    // },
    data() {
        return {
            addTaskForm: {
                id: 101,
                title: '',
                price: '',
                description: '',
                category: '',
                image: '',
                rating: {
                    count: '',
                    rate: '',
                },
            },
            isFormValid: false,
        };
    },
    // props: {
    //     task: Object,
    //     type: String,
    // },
    emits: ['add', 'close'],
    methods: {},
    // watch: {
    //     addTaskForm: {
    //         handler(newValue) {
    //             console.log([...newValue.value()].some((e) => e === ''));
    //         },
    //         deep: true,
    //     },
    // },
};
</script>

<style scoped>
.modal {
    position: fixed;
    width: 100vw;
    height: 100vh;
    background-color: rgba(0, 0, 0, 0.4);
    /* opacity: 0.4; */
    overflow: hidden;
    overflow-y: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
}
.modal-container {
    /* margin: auto auto; */
    background-color: #fff;
    border-radius: 25px;
    width: 500px;
    /* height: 500px; */
    padding: 50px;
}
.modal__form {
    display: flex;
    flex-direction: column;
}
.modal__form_submit-btn {
    height: 35px;
}
</style>
