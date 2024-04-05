<template>
<base-layout>
  <template v-if="response.completedExam && response.exam && response.owner">
    <ExamCard :response="response" :completed="true"/>

    <template v-if="response.qas">
      lllllllllll
      <QuestionList :qas="response.qas" />
    </template>
  </template>
</base-layout>
</template>

<script setup>

import { useRoute } from 'vue-router'
import axios from 'axios';
import { ref } from 'vue';
import { inject } from 'vue'
import { IonItem, IonLabel, IonList, IonListHeader, IonHeader } from '@ionic/vue'; //for the list 
import ExamCard from '@/components/ExamCard.vue';
import QuestionList from '@/components/QuestionList.vue';

import { onMounted } from 'vue';
import {useMainStore} from '@/stores/MainStore'

const store = useMainStore()

const URL_EXAM_ID = useRoute().params.examId
const API_URL = inject('API_URL')

const response = ref({})

onMounted(async () => {
  await getCompleted()
  await getExamAndOwner()
  await getAnsweredQAs()
  await getQAs()
})

async function getAnsweredQAs() {
  await axios.get(API_URL + '/api/completed/' + URL_EXAM_ID + '/qa').then(res => {
    // emit('uploaded', true)
    response.value.answeredQas=res.data
    console.log(response.value.qas)

  })
}

async function getQAs() {

  response.value.qas = []

  response.value.answeredQas.forEach(async (answeredQa) => {
    await axios.get(API_URL + '/api/completed/qa/' + answeredQa.qa_id).then(res => {
      let qa = res.data.data
      let qaNew = {
      id: null,
      question: null,
      answers: []
    }
    qaNew.id = qa.id
    qaNew.question = qa.question
    const responseAnswers = getCleanAndDecrypted([qa.ans_r, qa.ans_1, qa.ans_2, qa.ans_3, qa.ans_4, qa.ans_5], store.secretKey, store.iv)
    qaNew.answers = mixUp(responseAnswers)
    // qaNew.answers = lableAccordingly(responseAnswers)
    lableAccordingly(qaNew.answers)

    response.value.qas.push(qaNew)

      console.log(response.value.qas)
      
    })
  });
}

async function getCompleted(){
  await axios.get(API_URL + '/api/completed/' + URL_EXAM_ID).then(async res => {
    response.value.completedExam = res.data
    await getExamOwner()
  })
}

async function getExamAndOwner(){
  await axios.get(API_URL + '/api/exam/' + response.value.completedExam.exam_id).then(async res => {
    response.value.exam = res.data.data
    await getExamOwner()
  })
}


async function getExamOwner(){
  await axios.get(API_URL + '/api/user/' + response.value.completedExam.user_id).then(res => {
    response.value.owner = res.data
  })
}





function mixUp(arr){
  let lettersArr = ['a', 'b', 'c', 'd', 'e']
  let l = arr.length
  
  let output = {}

  for(let i = 0; i<l; i++){
    let letter = lettersArr[i]
    let randIndexInArr = Math.floor(Math.random()*arr.length)
    
    output[letter] = arr[randIndexInArr]
    arr.splice(randIndexInArr, 1)
  }
  return output
}

function lableAccordingly(arr){
  console.log('ffffffff', arr)
}

function getCleanAndDecrypted(arr, key, iv){
  const ansArr = []
  arr.forEach(value => {
    let valueD = getDAesString(value, key, iv)
    if(valueD) {
      ansArr.push(valueD)
    }
  })
  return ansArr
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

<style scoped>

</style>