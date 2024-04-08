<template>
  {{ all }}

  <ion-list>
    <ion-card v-for="one in all">
      <template v-if="one.question">
        <ion-card-header>
          <ion-card-title>{{ one.question }}</ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <ion-radio-group value="space-between" v-if="one.answers">
            <ion-item v-if="one.ans_r">
              <div class="rightAns">
                <ion-radio :value="one.ans_r" :name="one.id" justify="space-between" onChange="change()">{{ one.ans_r}}</ion-radio>
              </div>
            </ion-item>
            <ion-item v-for="ans in one.answers">
              <ion-radio :value="ans.val" :name="one.id" justify="space-between" onChange="change()" :checked="ans.selected">
                {{ ans.val }} {{ans.right ? '(Right Answer)' : '' }}
              </ion-radio>
            </ion-item>
          </ion-radio-group>
        </ion-card-content>
      </template>
      <template v-else-if="one.qas">
        <ion-card-header>
          <template v-for="qa in one.qas">
            <ion-card-title>{{ qa.question }}</ion-card-title>
          </template>
        </ion-card-header>
        <ion-card-content>
          <ion-item>

            <ion-item>
              <ion-list>
                <ion-reorder-group :disabled="false" @ionItemReorder="handleReorder($event)">
                  <template v-for="(qa, i) in one.qas">
                    <ion-item>
                      <!-- <ion-radio :value="qa.ans_r" :name="qa.id" justify="space-between" onChange="change()">{{qa.ans_r}}</ion-radio> -->
                      <ion-label> {{ alphabet[i] }} </ion-label>
                      <ion-reorder slot="end"></ion-reorder>
                    </ion-item>
                  </template>
                </ion-reorder-group>
              </ion-list>
            </ion-item>

            <ion-item slot="end">
              <ion-list>
                <ion-item v-for="qa in one.qas" :key="qa">
                  <ion-label> {{ qa.question }} </ion-label>
                </ion-item>
              </ion-list>
            </ion-item>
          </ion-item>
        </ion-card-content>
      </template>
    </ion-card>
  </ion-list>

</template>

<script setup>
import { IonItem, IonList, IonRadio, IonRadioGroup } from '@ionic/vue';
import { IonLabel, IonReorder, IonReorderGroup } from '@ionic/vue';

import { ref, onMounted, computed, onBeforeUpdate } from 'vue'


const props = defineProps({
  all: {
    type: Array,
    required: true
  }
})


const alphabet = ref(['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i'])

onMounted(async () => {
  // setTimeout(() => {
  console.log(props.all);
  // }, 3000)
})

const handleReorder = (event) => {
  // Before complete is called with the items they will remain in the
  // order before the drag
  console.log('Before complete', alphabet.value);

  // Finish the reorder and position the item in the DOM based on
  // where the gesture ended. Update the items variable to the
  // new order of items
  alphabet.value = event.detail.complete(alphabet.value);

  // After complete is called the items will be in the new order
  console.log('After complete', alphabet.value);
};
</script>

<style>
.correct {
  background-color: chartreuse;
  width: full;
  height: full;
}

.selected {
  background-color: red;
  border: solid black 2;
  width: full;
  height: full;
}

.rightAns {
  background-color: chartreuse;
  width: full;
  height: full;
}
</style>