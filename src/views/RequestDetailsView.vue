<script setup>
import { ref, computed, onMounted } from 'vue';
import { useRouter, useRoute } from 'vue-router';
import { useRemoteData } from '@/composables/useRemoteData.js';
import { useApplicationStore } from '@/stores/application.js';
const router = useRouter();
const route = useRoute();
const { userData } = useApplicationStore();



const studentIdRef = ref(null);
const urlRef = computed(() => {
    return 'http://localhost:9090/api/request/' + studentIdRef.value;
});


const authRef = ref(true);
const { data, loading, performRequest,getToken } = useRemoteData(urlRef, authRef);


onMounted(() => {
    studentIdRef.value = route.params.id;
    console.log("deutero check " + route.params.id);
    performRequest();
});



const approveRequest = () => {
    // Make the API call to approve the request
    // You can use the fetch function or a library like Axios

    // For example using fetch:
    fetch('http://localhost:9090/api/request/review/' + studentIdRef.value, {
        method: 'POST', // Adjust the method based on your API endpoint
        headers: {
            'Content-Type': 'application/json',
            'Authorization': getToken(),
            // 'Authorization': 'Bearer YOUR_ACCESS_TOKEN',
        },
        // You may need to send a request body if required by your API
         body: JSON.stringify({"action":"approved"}),
    })
    .then(response => {
        if (response.ok) {
            // Handle success, you may want to update your UI or perform other actions
            console.log('Request approved successfully');
        } else {
            // Handle errors
            console.error('Failed to approve request');
        }
    })
    .catch(error => {
        console.error('Error while approving request:', error);
    });
};


const denyRequest = () => {
    // Make the API call to approve the request
    // You can use the fetch function or a library like Axios

    // For example using fetch:
    fetch('http://localhost:9090/api/request/review/' + studentIdRef.value, {
        method: 'POST', // Adjust the method based on your API endpoint
        headers: {
            'Content-Type': 'application/json',
            'Authorization': getToken(),
            // 'Authorization': 'Bearer YOUR_ACCESS_TOKEN',
        },
        // You may need to send a request body if required by your API
        body: JSON.stringify({"action":"denied"}),
    })
    .then(response => {
        if (response.ok) {
            // Handle success, you may want to update your UI or perform other actions
            console.log('Request approved successfully');
        } else {
            // Handle errors
            console.error('Failed to approve request');
        }
    })
    .catch(error => {
        console.error('Error while approving request:', error);
    });
};

</script>
<template>

    <div class="bg-body-tertiary">
        <div class="container">
            <div class="mb-4">
                <div class="col-12">
                    <h1 class="fs-3"  v-if="userData.roles.includes('ROLE_REPRESENTATIVE')">Access Denied</h1>
                    <RouterLink class="small" :to="{ name: 'requests' }" v-if="userData.roles.includes('ROLE_TAXMAN')" >Back to Requests</RouterLink>
                    <h1 class="fs-3" v-if="userData.roles.includes('ROLE_TAXMAN')" >Request #{{ studentIdRef }}</h1>

                    <table class="table" v-if="userData.roles.includes('ROLE_TAXMAN')">
                        <thead>
                            <tr>
                                <th>Request Id</th>
                                <th>Name</th>
                                <th>Email</th>
                                <th>Goal</th>
                                <th>AOF</th>
                                <th>HQ</th>
                                <th>Representative</th>
                                <th>Executives</th>
                                <th>Action</th>
                                <th></th>
                            </tr>
                        </thead>
                        <tbody v-if="loading">
                            <tr>
                                <td colspan="8">Loading...</td>
                            </tr>
                        </tbody>
                        <tbody v-if="data">
                            <tr>
                                <td>{{ data.id }}</td>
                                <td>{{ data.company.name }}</td>
                                <td>{{ data.company.email }}</td>
                                <td>{{ data.company.goal }}</td>
                                <td>{{ data.company.articles_of_association }}</td>
                                <td>{{ data.company.hq }}</td>
                                <td>{{ data.company.representative.username }}</td>
                                <td>{{ data.company.executives }}</td>
                                <td><button @click="approveRequest">Approve</button></td>
                                <td><button @click="denyRequest">Deny</button></td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
        <div>
        </div>
    </div>
</template>

