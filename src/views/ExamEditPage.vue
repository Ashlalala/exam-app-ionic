<template>
  <base-layout title="Edit Exam">
    <ion-card>
      <template v-if="response.exam && response.owner">
        <ion-card-header>
          <ion-card-title :router-link="'/exam/' + URL_EXAM_ID">{{ response.exam.name }}</ion-card-title>
          <ion-card-subtitle><router-link :to="'/exams/' + response.exam.category">{{ firstBig(response.exam.category)
              }}
            </router-link> >
            <router-link :to="'/exams/' + response.exam.category + '/' + response.exam.sub_category">{{
        firstBig(response.exam.sub_category) }}
            </router-link></ion-card-subtitle>
        </ion-card-header>

        <ion-card-content>
          <p>{{ response.exam.description }}</p>
          <hr>
          <div class="flex justify-between bg-red-500">
            <ion-card-subtitle>
              Created by: <router-link :to="'/users/' + response.owner.id">{{ response.owner.name }} </router-link>
            </ion-card-subtitle>
            <ion-card-subtitle>{{ response.exam.created_at }}</ion-card-subtitle>
          </div>

        </ion-card-content>
      </template>
    </ion-card>

    <ion-button expand="block" fill="outline" :router-link="URL_EXAM_ID + '/edit'">Save</ion-button>

    <ion-button @click.prevent="popoverOpen = !popoverOpen" id="click-trigger" expand="block">Add Question</ion-button>
    <ion-popover :is-open="popoverOpen" trigger="click-trigger" trigger-action="click" size="cover">
      <ion-content class="ion-padding">
        <CreateQuestion @uploaded="uploaded" />
      </ion-content>
    </ion-popover>

    <template v-if="response.qas">
      <QuestionList :qas="response.qas" />
    </template>
  
  </base-layout>
</template>

<script setup>
import { useRoute } from 'vue-router'
import axios from 'axios';
import { onMounted } from 'vue';
import { ref } from 'vue';
import { inject } from 'vue';
import { IonCard, IonCardContent, IonCardHeader, IonCardSubtitle, IonCardTitle } from '@ionic/vue';
import { IonButton, IonContent, IonPopover } from '@ionic/vue';//for popover

import CreateQuestion from '@/components/CreateQuestion.vue'
import QuestionList from '@/components/QuestionList.vue'


const popoverOpen = ref(false)


const routeParams = useRoute().params
const API_URL = inject('API_URL')
const URL_EXAM_ID = routeParams.examId

const response = ref({})



function uploaded() {
  popoverOpen.value = false
  getQAs()
}



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
  await axios.get(API_URL + '/api/exam/' + URL_EXAM_ID).then(async (res) => {
  console.log(res)
  response.value.exam = res.data.data
  await getExamOwner()
})
}


async function getExamOwner() {
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