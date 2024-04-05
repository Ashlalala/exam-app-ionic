<template>
  <form @submit.prevent="formUpload">
    <ion-list>
      <ion-item>
        <ion-input v-model="form.question" label="Question *" autofocus placeholder="Enter the question"></ion-input>
      </ion-item>
      <ion-item>
        <ion-input v-model="form.ans_r" label="Right answer *" placeholder="Enter the right answer"></ion-input>
      </ion-item>
      <ion-item>
        <ion-input v-model="form.ans_1" label="Wrong answer 1 *" placeholder="Enter wrong answer 1"></ion-input>
      </ion-item>
      <ion-item v-if="form.ans_1">
        <ion-input v-model="form.ans_2" label="Wrong answer 2" placeholder="Enter wrong answer 2"></ion-input>
      </ion-item>
      <ion-item v-if="form.ans_2">
        <ion-input v-model="form.ans_3" label="Wrong answer 3" placeholder="Enter wrong answer 3"></ion-input>
      </ion-item>
      <ion-item v-if="form.ans_3">
        <ion-input v-model="form.ans_4" label="Wrong answer 4" placeholder="Enter wrong answer 4"></ion-input>
      </ion-item>
      <ion-item v-if="form.ans_4">
        <ion-input v-model="form.ans_5" label="Wrong answer 5" placeholder="Enter wrong answer 5"></ion-input>
      </ion-item>
    </ion-list>
    <ion-button type="submit" @click.prevent expand="block">Submit</ion-button>
  </form>
</template>

<script setup>
import { useRoute } from 'vue-router'
import axios from 'axios';
import { ref } from 'vue';
import { inject } from 'vue';
import { IonButton, IonList, IonListHeader, IonInput, IonItem, } from '@ionic/vue'; //for the list 

const routeParams = useRoute().params
const API_URL = inject('API_URL')
const URL_EXAM_ID = routeParams.examId

const emit = defineEmits(['updated'])

const form = ref({
  question: null,
  ans_r: null,
  ans_1: null,
  ans_2: null,
  ans_3: null,
  ans_4: null,
  ans_5: null,
})

async function formUpload() {
  // form.value.sub_name = URL_SUBCAT
  // form.value.user_id = 1  // CHANGE THIS
  form.value.exam_id = URL_EXAM_ID
  form.value.type = 'full'


  console.log(form.value);

  try {
    await axios.get('http://localhost:8000/sanctum/csrf-cookie')

    // const userLogin = await JSON.parse(localStorage.getItem('userLogin'));

    await axios.post(API_URL + '/api/exam/' + URL_EXAM_ID + '/qa', form.value, {
      headers: {
        "Content-Type": "multipart/form-data",
        accept: 'application/json',
        // Authorization: 'Bearer ' + userLogin.token
      }
    }).then(res => {
      console.log(res)
      emit('uploaded', true)
    })
  } catch (e) {
    console.log(e);
  }
}
</script>
