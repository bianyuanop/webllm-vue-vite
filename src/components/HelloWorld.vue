<script lang="ts" setup>
import { ref, onMounted } from 'vue';
import * as webllm from '@mlc-ai/web-llm';
let newMsg = ref('');
let reportMsg = ref('');
let history = ref<HTMLElement | null>(null);

let model_name = 'RedPajama-INCITE-Chat-3B-v1-q4f32_0';

let chatBot = new webllm.ChatModule();
chatBot.setInitProgressCallback((report) => {
  reportMsg.value = report.text;
});

async function initBot() {
  await chatBot.reload(model_name);
}

function pushMessage(from: string, message: string, step: number) {
  if(history.value == null) return;

  let node = document.createElement('div');
  node.setAttribute('class', 'prompt');
  node.innerText = `${from}-${step}: ${message}`;

  history.value.appendChild(node);
}

function generateProgressCallBack(_step: number, message: string) {
  pushMessage('ChatBot', message, _step);
}

function interacBot(msg: string) {
  chatBot.generate(msg, generateProgressCallBack);
}

onMounted(async () => {
  try {
    await initBot();
  } catch (e) {
    console.log(e);
  }
});
</script>

<template>
  <div class="container">
    <div id="initialize">{{ reportMsg }}</div>
    <div id="history" ref="history">Scrollerbel</div>
    <div id="new chat">
      <textarea
        name="chat"
        id="new"
        cols="30"
        rows="10"
        :value="newMsg"
      ></textarea>
      <button @click="interacBot(newMsg)">Send</button>
    </div>
  </div>
</template>

<style>
#history {
  scroll-behavior: auto;
  height: 400px;
  border-width: 1px;
}
</style>
