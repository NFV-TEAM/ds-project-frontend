<script setup>
import { useApplicationStore } from '@/stores/application.js';
const { userData } = useApplicationStore();
</script>

<template>
    <div class="bg-body-tertiary">
        <div class="container">
            <div class="row py-4 px-3">
                <div class="col-12">
                    <div class="mb-4">
                        <h1 class="fs-3">Profile</h1>
                    </div>
                    <div>
                        <p>
                            <b>Username</b>
                            <pre>{{ userData.username }}</pre>
                            <b>Email</b>
                            <pre>{{ userData.email }}</pre>
                            <b>Role</b>
                            <p v-if="userData.roles.includes('ROLE_REPRESENTATIVE')">Representative</p>
                            <p v-if="userData.roles.includes('ROLE_TAXMAN')">Taxman</p>
                            
                        </p>
                        <!-- @EXERCISE: Create a nice component to present user data -->
                        
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
