<script setup lang="ts">
import { ref, computed, watch } from "vue";
import InputLabel from "./components/InputLabel.vue";
import TextInput from "./components/TextInput.vue";
import iconDollar from "./assets/icon-dollar.svg";
import iconPerson from "./assets/icon-person.svg";

const tips = [5, 10, 15, 25, 50];
const currentTip = ref(0);
const customTip = ref(50);
const showCustomTipInput = ref(false);
const bill = ref();
const numberOfPeople = ref();
const errors = ref<Record<string, string>>({});
const tipAmount = computed(() => {
  if (bill.value && numberOfPeople.value) {
    return (bill.value / numberOfPeople.value / 100) * currentTip.value;
  }
  return 0;
});
const total = computed(() => {
  if (bill.value && numberOfPeople.value) {
    return (bill.value / numberOfPeople.value / 100) * currentTip.value + bill.value / numberOfPeople.value;
  }
  return 0;
});

function applyCustomTip() {
  if (customTip.value >= 5 && customTip.value <= 70) {
    currentTip.value = customTip.value;
  } else {
    customTip.value = 50;
  }
  showCustomTipInput.value = false;
}

function reset() {
  bill.value = null;
  numberOfPeople.value = null;
}

function setError(name: string, message: string) {
  errors.value[name] = message;
}

watch(bill, (newBill) => {
  newBill === 0 ? setError("bill", "Can't be zero") : setError("bill", "");
});

watch(numberOfPeople, (newNumber) => {
  newNumber === 0 ? setError("numberOfPeople", "Can't be zero") : setError("numberOfPeople", "");
});
</script>

<template>
  <div class="w-full bg-neutral-200 min-h-screen p-2 flex flex-col gap-8 items-center justify-center">
    <div class="w-16 h-16">
      <img class="w-full h-auto" src="/src/assets/logo.svg" alt="" />
    </div>
    <div class="rounded-2xl flex flex-wrap max-w-2xl w-full gap-8 bg-white shadow p-6">
      <form @submit.prevent class="flex flex-1 p-2 flex-col gap-10">
        <div class="flex flex-col gap-1">
          <div class="flex justify-between items-center">
            <InputLabel for="bill">Bill</InputLabel>
            <span class="text-xs font-bold text-red-600">{{ errors.bill }}</span>
          </div>
          <TextInput id="bill" name="bill" :error="errors.bill" v-model.number="bill">
            <template #prepend>
              <span class="inline-flex justify-center px-2 pl-3 items-center">
                <img class="w-2" :src="iconDollar" alt="" />
              </span>
            </template>
          </TextInput>
        </div>
        <div>
          <InputLabel>Select Tip %</InputLabel>
          <div class="grid grid-rows-2 mt-2 gap-2 grid-cols-3">
            <button
              @click="currentTip = tip"
              :class="[
                'text-white text-lg bg-neutral-500 py-1 inline-flex justify-center items-center rounded',
                currentTip === tip ? 'bg-primary text-neutral-500' : 'hover:bg-neutral-200',
              ]"
              v-for="(tip, i) in tips"
              :key="i"
            >
              {{ tip }}%
            </button>
            <button
              class="font-bold text-lg text-neutral-400 bg-neutral-100 rounded"
              v-if="!showCustomTipInput"
              @click="showCustomTipInput = true"
            >
              Custom
            </button>
            <div ref="customTipInput" class="flex gap-2 items-center" v-else>
              <TextInput v-model.number="customTip" @keydown.enter="applyCustomTip" class="w-full" min="5" max="70" />
            </div>
          </div>
        </div>

        <div class="flex flex-col gap-1">
          <div class="flex justify-between items-center">
            <InputLabel for="numberOfPeople">Number of people</InputLabel>
            <span class="text-xs font-bold text-red-600">{{ errors.numberOfPeople }}</span>
          </div>
          <TextInput id="numberOfPeople" name="numberOfPeople" :error="errors.numberOfPeople" v-model.number="numberOfPeople">
            <template #prepend>
              <span class="inline-flex justify-center px-2 pl-3 items-center">
                <img class="w-2" :src="iconPerson" alt="" />
              </span>
            </template>
          </TextInput>
        </div>
      </form>
      <div class="bg-neutral-500 font-bold rounded-xl flex-1 px-4 pt-12 pb-8">
        <div class="flex px-4 h-full flex-col gap-8 justify-between">
          <div class="space-y-10">
            <div class="flex justify-between items-center">
              <div>
                <p class="text-xs text-white">Tip Amount</p>
                <p class="text-[10px] text-neutral-300">/ person</p>
              </div>
              <div>
                <p class="text-4xl text-primary">${{ tipAmount.toFixed(2) }}</p>
              </div>
            </div>
            <div class="flex justify-between items-center">
              <div>
                <p class="text-xs text-white">Total</p>
                <p class="text-[10px] text-neutral-300">/ person</p>
              </div>
              <div>
                <p class="text-4xl text-primary">${{ total.toFixed(2) }}</p>
              </div>
            </div>
          </div>
          <div>
            <button
              :disabled="!bill || !numberOfPeople"
              @click="reset"
              class="uppercase py-1 bg-primary disabled:bg-primary/20 hover:bg-neutral-200 text-neutral-500 rounded w-full"
            >
              reset
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
