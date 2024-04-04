<template>
<base-layout title="Exams">
  <ion-card>
    <template v-if="response.exam && response.owner">
      <ion-card-header>
        <ion-card-title>{{ response.exam.name }}</ion-card-title>
        <ion-card-subtitle><router-link :to="'/exams/' + response.exam.category" >{{firstBig(response.exam.category)}} </router-link> >
        <router-link :to="'/exams/' + response.exam.category + '/' + response.exam.sub_category" >{{firstBig(response.exam.sub_category)}} </router-link></ion-card-subtitle>
      </ion-card-header>
      
      <ion-card-content>
        <p>{{ response.exam.description }}</p>
        <hr>  
        <div class="flex justify-between bg-red-500">
          <ion-card-subtitle>
            Created by: <router-link :to="'/users/' + response.owner.id" >{{response.owner.name}} </router-link>
          </ion-card-subtitle>
          <ion-card-subtitle>{{ response.exam.created_at }}</ion-card-subtitle>
        </div>
        
      </ion-card-content>
    </template>
  </ion-card>
  
  <template v-if="response.qas">
    <ion-button expand="block" v-if="response.qas.length" :router-link="URL_EXAM_ID + '/take'">Take Exam</ion-button>
    <ion-button expand="block" v-else>The Exam is Empty</ion-button>
    <ion-button expand="block" fill="outline" :router-link="URL_EXAM_ID + '/edit'">Add/Remove/Edit Questions</ion-button>
  </template>

</base-layout>
</template>

<script setup>

import { useRoute } from 'vue-router'
import axios from 'axios';
import { ref } from 'vue';
import { inject } from 'vue';
import { onMounted } from 'vue';

import { IonCard, IonCardContent, IonCardHeader, IonCardSubtitle, IonCardTitle, IonButton } from '@ionic/vue';

const routeParams = useRoute().params
const API_URL = inject('API_URL')
const URL_EXAM_ID = routeParams.examId

const response = ref({})

onMounted(async () => {
  await getExamAndOwner()
  await getQAs()
})

async function getQAs() {
  await axios.get(API_URL + '/api/exam/' + URL_EXAM_ID + '/qa').then(res => {
    console.log(res)
    // emit('uploaded', true)
    response.value.qas=res.data.data
  })
}

async function getExamAndOwner(){
  await axios.get(API_URL + '/api/exam/' + URL_EXAM_ID).then(async res => {
    console.log(res)
    response.value.exam = res.data.data
    await getExamOwner()
  })
}


async function getExamOwner(){
  await axios.get(API_URL + '/api/user/' + response.value.exam.user_id).then(res => {
    console.log(res);
    response.value.owner = res.data
    console.log(response.value.owner)
  })
}


function firstBig(string) {
  return string.charAt(0).toUpperCase() + string.slice(1);
}


</script>

