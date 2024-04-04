<template>
  <ion-list>
    <ion-card v-for="qa in qas">
      <ion-card-header>
        <ion-card-title>{{ qa.question }}</ion-card-title>
      </ion-card-header>

      <ion-card-content>
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
</script>

