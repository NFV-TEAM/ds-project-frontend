<script setup>
import { onMounted, ref } from 'vue';
import { useRemoteData } from '@/composables/useRemoteData.js';
import { useApplicationStore } from '@/stores/application.js';
const urlRef = ref('http://localhost:9090/api/request/pending');  // Adjust the URL based on your API endpoint
const authRef = ref(true);
const { data, loading, performRequest } = useRemoteData(urlRef, authRef);

const { userData } = useApplicationStore();
onMounted(() => {
    performRequest();
});
</script>

<template>
    <div class="bg-body-tertiary">
        <div class="container">
            <div class="row py-4 px-3">
                <div class="col-12">
                    <div class="mb-4">
                        <h1 class="fs-3 " v-if="userData.roles.includes('ROLE_TAXMAN')">Requests</h1>
                        <h1 class="fs-3" v-if="userData.roles.includes('ROLE_REPRESENTATIVE')">Access Denied</h1>
                        
                    </div>
                    <div>
                        <table class="table"  v-if="userData.roles.includes('ROLE_TAXMAN')">
                            <thead>
                                <tr>
                                    <th>Id</th>
                                    <th>Name</th>
                                    <th>Email</th>
                                    <th>Goal</th>
                                    <th>AOF</th>
                                    <th>HQ</th>
                                    <th>Representative</th>
                                    <th>Executives</th>
                                    <th>Action</th>
                                </tr>
                            </thead>
                            <tbody v-if="loading">
                                <tr>
                                    <td colspan="8">Loading...</td>
                                </tr>
                            </tbody>
                            <tbody v-if="data">
                                <tr v-for="request in data">
                                    <td>{{ request.id }}</td>
                                    <td>{{ request.company.name }}</td>
                                    <td>{{ request.company.email }}</td>
                                    <td>{{ request.company.goal }}</td>
                                    <td>{{ request.company.articles_of_association }}</td>
                                    <td>{{ request.company.hq }}</td>
                                    <td>{{ request.company.representative.username }}</td>
                                    <td>{{ request.company.executives }}</td>
                                    <td>
                                        <RouterLink :to="{
                                                name: 'request-details',
                                                params: { id: request.id }
                                            }">Display</RouterLink>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>