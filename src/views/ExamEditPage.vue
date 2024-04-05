<template>
  <base-layout title="Edit Exam">
  <ion-popover :is-open="popoverOpen" trigger="click-trigger" trigger-action="click" size="cover" side="bottom" alignment="center">
      <ion-content class="ion-padding">
        <CreateQuestion @uploaded="uploaded" />
      </ion-content>
    </ion-popover>
    <ion-button @click.prevent="popoverOpen = !popoverOpen" id="click-trigger" expand="block" class="footerBtn">Add Question</ion-button>
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
import {useMainStore} from '@/stores/MainStore'



import CreateQuestion from '@/components/CreateQuestion.vue'
import QuestionList from '@/components/QuestionList.vue'

const store = useMainStore()

const popoverOpen = ref(false)


const routeParams = useRoute().params
const API_URL = inject('API_URL')
const URL_EXAM_ID = routeParams.examId

const response = ref({
  qas: []
})



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
    response.value.qas = []
    handleQAs(res.data.data)
  })
}


function handleQAs(r){
  r.forEach(qa => {
    let qaNew = {
      question: null,
      answers: []
    }
    qaNew.question = qa.question
    qaNew.answers = getCleanAndDecrypted([qa.ans_r, qa.ans_1, qa.ans_2, qa.ans_3, qa.ans_4, qa.ans_5], store.secretKey, store.iv) // The right answer should always be first in the arr
    response.value.qas.push(qaNew)
  })
}


  
function getCleanAndDecrypted(arr, key, iv){
  const ansArr = []
  arr.forEach((value, index) => {
    let valueDecrypted = getDAesString(value, key, iv)
    if(valueDecrypted) {
      let ansObj = {
        val: valueDecrypted,
        right: index == 0 
      }
      ansArr.push(ansObj)
    }
  })
  return ansArr
}

async function getExamAndOwner(){
  await axios.get(API_URL + '/api/exam/' + URL_EXAM_ID).then(async (res) => {
  response.value.exam = res.data.data
  await getExamOwner()
})
}


async function getExamOwner() {
  await axios.get(API_URL + '/api/user/' + response.value.exam.user_id).then(res => {
    response.value.owner = res.data
  })
}



function getDAesString(encrypted, key, iv) {//解密
    var key_hash = CryptoJS.MD5(key).toString();
    var key = CryptoJS.enc.Utf8.parse(key_hash);
    var iv = CryptoJS.enc.Utf8.parse(iv);
    var decrypted = CryptoJS.AES.decrypt(encrypted, key,
        {
            iv: iv,
            mode: CryptoJS.mode.CBC,
            padding: CryptoJS.pad.Pkcs7
        });
    return decrypted.toString(CryptoJS.enc.Utf8);
}
function firstBig(string) {
  return string.charAt(0).toUpperCase() + string.slice(1);
}
</script>

<style>

</style>
