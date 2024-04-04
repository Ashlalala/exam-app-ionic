<template>
  <base-layout :title="response.exam ? response.exam.name : ''">
    <template v-if="response.qas" v-for="qa in response.qas[store.examPage]">
      <ion-radio-group value="space-between">
        <ion-item v-if="qa.ans_r">
          <ion-radio :value="qa.ans_r" :name="qa.id" justify="space-between" onChange="change()">{{qa.ans_r}}</ion-radio>
        </ion-item>
        <ion-item v-if="qa.ans_w_1">
          <ion-radio :value="qa.ans_w_1" :name="qa.id" justify="space-between" onChange="change()">{{qa.ans_w_1}}</ion-radio>
        </ion-item>
        <ion-item v-if="qa.ans_w_2">
          <ion-radio :value="qa.ans_w_2" :name="qa.id" justify="space-between" onChange="change()">{{ qa.ans_w_2 }}</ion-radio>
        </ion-item>
        <ion-item v-if="qa.ans_w_3">
          <ion-radio :value="qa.ans_w_3" :name="qa.id" justify="space-between" onChange="change()">{{ qa.ans_w_3 }}</ion-radio>
        </ion-item>
        <ion-item v-if="qa.ans_w_4">
          <ion-radio :value="qa.ans_w_4" :name="qa.id" justify="space-between" onChange="change()">{{ qa.ans_w_4 }}</ion-radio>
        </ion-item>
      </ion-radio-group>
    </template>
    <div class="footerBtn">
      <ion-button expand="full" @click.prevent="submitExam">Submit Exam</ion-button>
      <ion-button expand="full" fill="outline" @click.prevent="cancelExam">Cancel Exam</ion-button>
    </div>
  </base-layout>
</template>

<script setup>
import QuestionList from '@/components/QuestionList.vue'
import { IonCard, IonCardContent, IonCardHeader, IonCardSubtitle, IonCardTitle, IonButton } from '@ionic/vue';

import { useRoute } from 'vue-router'
import axios from 'axios';
import { ref } from 'vue';
import { inject } from 'vue';
import { onMounted } from 'vue';
import {useMainStore} from '@/stores/MainStore'

const store = useMainStore()

const routeParams = useRoute().params
const API_URL = inject('API_URL')
const URL_EXAM_ID = routeParams.examId

const response = ref({})

const form = ref({

})

const page = ref(1)

function submitExam(){
  console.log(form.value)
}
function cancelExam(){
  for(qa in response.value.qas[store.examPage]){

    console.log(qa)
  }
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

<style>
.footerBtn {
  position: sticky;
  left: 0;
  bottom: 0;
  width: 100%;
  text-align: center;
  background-color: white
}
</style>