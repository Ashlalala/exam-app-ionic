<template>
  {{ qas }}
  <ion-list>
    <ion-card v-for="qa in qas">
      <ion-card-header>
        <ion-card-title>{{ qa.question }}</ion-card-title>
      </ion-card-header>

      <ion-card-content>
        <ion-radio-group value="space-between" v-if="qa.answers">
          <ion-item v-if="qa.ans_r">
            <div class="rightAns">
              <ion-radio :value="qa.ans_r" :name="qa.id" justify="space-between" onChange="change()">{{qa.ans_r}}</ion-radio>
            </div>
          </ion-item>
          <ion-item v-for="ans in qa.answers">
            {{ ans }}
            <ion-radio :value="ans.val" :name="qa.id" justify="space-between" onChange="change()">{{ans.val}}  {{ ans.right ? '(Right Answer)' : '' }}</ion-radio>
          </ion-item>
          
        </ion-radio-group>
      </ion-card-content>
    </ion-card>
  </ion-list>

</template>

<script setup>
import { IonItem, IonList, IonRadio, IonRadioGroup } from '@ionic/vue';

const props = defineProps({
  qas: {
    type: Array,
    required: true
  }
})

console.log('liiiiiiiiiiiiiiiiststtstst', props.qas)
</script>

<style>
.rightAns{
  background-color: chartreuse;
  width: full;
  height: full;

}
</style>