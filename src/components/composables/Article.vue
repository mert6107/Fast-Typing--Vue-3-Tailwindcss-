<template>
  <div v-if="isFinish" class="flex flex-col items-center mt-4" >
    <span class="text-4xl bg-neutral-600 text-white rounded-md p-3 w-1/3">GAME OVER</span>
    <div class="flex flex-col mt-4">
      <span class="text-2xl bg-green-400 text-white rounded p-2 mt-2">Correct words: {{trueCount}}</span>
      <span class="text-2xl bg-red-400 text-white rounded p-2 mt-2">Wrong words: {{falseCount}}</span>
      <span class="text-2xl bg-gray-400 text-white rounded p-2 mt-2">Accuracy: %{{truePercent}}</span>
      <span class="text-2xl bg-gray-400 text-white rounded p-2 mt-2">Keystrokes: {{dks}}</span>

    </div>
    <button class="w-1/3 bg-green-300 text-2xl text-white rounded-md mt-4 p-2" @click="newGame">Try Again</button>

  </div>
  <div v-else class="bg-gray-200 flex flex-col items-center">
    <div class="bg-white w-3/4 rounded-md mt-6 ">
      <p class="p-6 text-gray-600 flex flex-wrap">
        <span v-for="(words,key) in words.filter((data,index)=>index<15 && data.length > 3)" :key="key"  class="ml-1 text-2xl " :class="key != 0 || changeColor" >{{words}} </span>
      </p>
    </div>
    <div class="rounded-md w-3/4 mt-2 bg-white ">
      <div class="rounded-md p-4 bg-slate-400	">

        <input class="w-5/6 p-2 border-solid border-2 border-gray rounded-md" type="text" v-model="writingWord" >
        <button class="p-2 border-2 border-gray rounded-md bg-gray-400 text-white" disabled>{{maxTime}}sn</button>
        <button  class="p-2 border-2 border-gray rounded-md bg-red-600 text-white ml-2" :disabled="isRunning" @click="getWords">Retry</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import wordLists from "../../assets/words.json";
import {ref,computed, watch, onMounted} from "vue";


const words = ref([]);
const writingWord = ref('');
const bgGray =ref(true);
const trueCount = ref(0);
const falseCount = ref(0);
const wordList = wordLists;

const maxTime = ref(60);
const isRunning = ref(false);
const timer = ref(0);
const isFinish = ref(false);

const deneme = onMounted(()=>{
  getWords();
});

const getWords = onMounted(() => {
  words.value = wordList.sort(()=>Math.random()-0.5).splice(0,300);
});

watch(() => writingWord.value,(atTime, prevTime) => {
  if(writingWord.value === 0 || writingWord.value === ' '){
    return writingWord.value = ''
  }
  if(!isRunning.value)counterTime();
  const correctWorld = words.value[0].slice(0,writingWord.value.length).toUpperCase();
  const myWorld = writingWord.value.replace(' ','').toUpperCase();
  bgGray.value = myWorld === correctWorld;

  if(writingWord.value.indexOf(' ') !== -1){
    bgGray.value ? trueCount.value++ : falseCount.value++;
    words.value.splice(0,1);
    writingWord.value = '';
  }
});

const dks = computed(()=>{
  return 300 - (words.value.length);
});

const truePercent = computed(()=>{
  const percent = (100/dks.value);
  const val = (percent * trueCount.value).toFixed(2);
  return isNaN(val) ? 0 : val;
});

const counterTime = () => {
  isRunning.value = true;

  timer.value = setInterval(()=> {
    decreaseTime();
  },1000);
};

const decreaseTime = () => {
  if(maxTime.value === 0){
    isFinish.value = true;
    return clearInterval(timer.value);
  }
  maxTime.value--;
};

const changeColor = computed(()=>{
  return bgGray.value ? 'bg-gray-400 text-white p-1 rounded' : 'bg-red-400 text-white p-1 rounded';
});

const newGame = ()=>{
   getWords();
   isFinish.value = false;
   maxTime.value = 60;
   isRunning.value = false;
   bgGray.value = true;
};



</script>
