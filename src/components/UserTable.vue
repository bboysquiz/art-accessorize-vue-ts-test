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
const sortAscending: Ref<boolean> = ref(true);


const filteredUsers = computed(() => {
    let result = users.filter(user => user.name.toLowerCase().includes(search.value.toLowerCase()));
    if (!sortAscending.value) {
        result = result.slice().sort((a, b) => b.age - a.age);
    } else {
        result = result.slice().sort((a, b) => a.age - b.age);
    }
    return result
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
    if (action === 'prev') {
        currentPage.value--
    } else if (action === 'next') {
        currentPage.value++
    }
}

const handleSearchChange = () => {
    currentPage.value = 1;
}

const sortByAge = () => {
    sortAscending.value = !sortAscending.value;
}

</script>

<template>
    <div class="content">
        <div class="header">
            <input class="search" type="text" v-model="search" @input="handleSearchChange">
            <button class="sort-button" @click="sortByAge">
                sort by age 
                <img class='sort-arrow' src="../assets/arrow.png" :style="{ transform: sortAscending ? 'rotateZ(-90deg)' : 'rotateZ(90deg)' }" alt="arrow">
            </button>
        </div>

        <table class="table">
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
            <button class="pagination-el" v-for="item in pageNumbers" :key="item" @click="gotoPage(item)">{{ item
            }}</button>
            <button @click="changePage('next')" :disabled="currentPage === totalPages">Next</button>
        </div>
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

.sort-arrow {
    width: 25px;
    height: 25px;
}

.sort-button {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.header {
    display: flex;
    justify-content: space-around;
    align-items: center;
    width: 100%;
}

.search {
    width: 300px;
}

.table {
    width: 1000px;
}

.table td {
    width: 200px;
}
.content {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    width: 1000px;
}
</style>
