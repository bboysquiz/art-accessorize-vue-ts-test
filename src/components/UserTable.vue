<script setup lang="ts">
import { ref, computed, Ref } from 'vue';
import userData from '../data'
import { User, PageAction } from '../types';


const users: User[] = userData;
const currentPage: Ref<number> = ref(1);
const search: Ref<string> = ref('');
const perPage: number = 5;
const startPage: Ref<number> = ref(0);
const endPage: Ref<number> = ref(5);


const filteredUsers = computed(() => {
    return users.filter(user => user.name.toLowerCase().includes(search.value.toLowerCase()))
})

const totalPages = computed(() => {
    return Math.ceil(filteredUsers.value.length / perPage);
});

const pageNumbers = computed(() => {
    return Array.from({ length: totalPages.value }, (_, index) => index + 1);
});

const displayedUsers = computed(() => {
    startPage.value = (currentPage.value - 1) * perPage;
    endPage.value = startPage.value + perPage;
    return filteredUsers.value.slice(startPage.value, endPage.value);
});

const gotoPage = (pageNumber: number) => {
    currentPage.value = pageNumber
};
const changePage = (action: PageAction) => {
    if (action === 'prev'){
        currentPage.value--
    }else if(action === 'next'){
        currentPage.value++
    }
}

const handleSearchChange = () => {
    currentPage.value = 1;
}

</script>

<template>
    <input type="text" v-model="search" @input="handleSearchChange">
    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Age</th>
                <th>Last Login</th>
                <th>Actions</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="user in displayedUsers" :key="user.id">
                <td>{{ user.id }}</td>
                <td>{{ user.name }}</td>
                <td>{{ user.age }}</td>
                <td>{{ user.last_login }}</td>
                <td>
                    <button>Edit</button>
                    <button>Delete</button>
                </td>
            </tr>
        </tbody>
    </table>
    <div class="pagination">
        <button @click="changePage('prev')" :disabled="currentPage === 1">Prev</button>
        <button class="pagination-el" v-for="item in pageNumbers" :key="item" @click="gotoPage(item)">{{ item }}</button>
        <button @click="changePage('next')" :disabled="currentPage === totalPages">Next</button>
    </div>
</template>

<style scoped>
.pagination {
    display: flex;
    width: 500px;
    justify-content: space-between;
    align-items: center;
}

.pagination-el {
    cursor: pointer;
    background-color: grey;
    width: 30px;
    height: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 10px;
}
</style>
