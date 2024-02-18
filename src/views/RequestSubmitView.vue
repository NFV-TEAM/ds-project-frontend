<script setup>
import { useApplicationStore } from '@/stores/application.js';
const { userData } = useApplicationStore();
const isFormSubmitted = ref(false);
import { ref, onMounted } from 'vue';


const isFormSubmittedInSession = () => {
  const submitted = localStorage.getItem('isformSubmitted');
  return submitted === 'true';
};


onMounted(() => {
  isFormSubmitted.value = isFormSubmittedInSession();
});

const formData = {
  name: '',
  email: '',
  aof: '',
  hq: '',
  executives: '',
};

const submitRequest = async () => {
  try {
    const response = await fetch('http://localhost:9090/api/submit', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + userData.accessToken,
      },
      body: JSON.stringify({
        company_name: formData.name,
        company_email: formData.email,
        goal: formData.goal, 
        articles_of_association: formData.aof,
        hq: formData.hq,
        executives: formData.executives,
      }),
    });

    if (response.ok) {
      const responseData = await response.json();
      console.log('Request submitted successfully:', responseData);
      isFormSubmitted.value = true;
    } else {
      console.error('Error submitting request:', response.status, response.statusText);
    }
  } catch (error) {
    console.error('Error submitting request:', error);
  }
};
</script>

<template>
  <div class="bg-body-tertiary">
    <div class="container">
      <div class="row py-4 px-3">
        <div class="col-12">
          <div class="mb-4"></div>
          <div>
            <p v-if="userData.roles.includes('ROLE_REPRESENTATIVE')">
              <h2 v-if="!isFormSubmitted">Make a request</h2>

              <form @submit.prevent="submitRequest" v-if="!isFormSubmitted">
                <label for="name">Name:</label>
                <input v-model="formData.name" type="text" id="name" name="name" required>

                <label for="email">Email:</label>
                <input v-model="formData.email" type="email" id="email" name="email" required>

                <label for="aof">AOF:</label>
                <input v-model="formData.aof" type="text" id="aof" name="aof" required>

                <label for="goal">GOAL:</label>
                <input v-model="formData.goal" type="text" id="goal" name="goal" required>

                <label for="hq">HQ:</label>
                <input v-model="formData.hq" type="text" id="hq" name="hq" required>

                <label for="executives">Executives:</label>
                <input v-model="formData.executives" type="text" id="executives" name="executives" required>

                <input type="submit" value="Submit Request">
                <p v-if="isFormSubmitted">Your request was sent successfully!</p>
              </form>
            </p>
            <p v-if="userData.roles.includes('ROLE_TAXMAN')">Access Denied</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>



<style scoped>
     form {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        label {
            margin-bottom: 5px;
        }

        input, select {
            width: 250px; /* Adjust the width as needed */
            margin-bottom: 10px;
        }

        input[type="submit"] {
            width: auto;
        }
</style>


