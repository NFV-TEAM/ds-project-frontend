<template>
    <div class="bg-body-tertiary">
        <div class="container">
            <div class="row py-4 px-3">
                <div class="col-12">
                    <div class="mb-4">
                        <h1 class="fs-3">Home</h1>
                    </div>
                    <div>
                        <p>
                            Logged in as: <strong>{{ userData.username }}</strong>
                        </p>
                        <p v-if="applicationStore.hasRole('ROLE_REPRESENTATIVE') && !responseData">
                            <RouterLink :to="{ name: 'submit' }">Click here to make a new request for your company!</RouterLink>    
                        </p>
                        <p v-if="applicationStore.hasRole('ROLE_TAXMAN')">
                            <RouterLink :to="{ name: 'requests' }">Click here to manage pending requests!</RouterLink>
                        </p>

                        <p v-if="applicationStore.hasRole('ROLE_REPRESENTATIVE')">
                            <table class="table" v-if="responseData">
                                <tr>
                                    <th>Company</th>
                                    <th>Tax ID</th>
                                    <th>State</th>
                                </tr>
                                <tr>
                                    <td>{{ responseData.request.company.name }}</td>
                                    <td>{{ responseData.request.company.taxID }}</td>
                                    <td>{{ responseData.request.state }}</td>
                                </tr>
                            </table>
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
    import {
        useApplicationStore
    } from '@/stores/application.js';
    import {
        ref
    } from 'vue';
    import {
        onMounted
    } from 'vue';
    const {
        userData
    } = useApplicationStore();
    const applicationStore = useApplicationStore();
    const responseData = ref(null);

    const checkRequest = async () => {
        try {
            const response = await fetch('http://localhost:9090/api/myrequest', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': 'Bearer ' + userData.accessToken,
                },
                body: JSON.stringify({
                    id: userData.id
                }),
            });

            if (response.ok) {
                responseData.value = await response.json();
                console.log('Data retrieved successfully:', responseData);
            } else {
                console.error('Error retrieving data:', response.status, response.statusText);
            }
        } catch (error) {
            console.error('Error', error);
        }
    };

    // Call checkRequest when the component is mounted
    onMounted(() => {
        checkRequest();
    });
</script>