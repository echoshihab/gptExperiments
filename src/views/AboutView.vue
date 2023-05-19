


<template>
  <div class="container">
    <v-textarea label="Patient Condition" v-model="prompt" rows="4" cols="50"></v-textarea>
    <div>
    <v-btn @click="submitPrompt" :loading="loading">Submit</v-btn>
  </div>
  <div class="stat-card" v-if="promptFlag">
    <StatCard :stat=isStat :reason=statReason :prompt=prompt />
  </div>
  
  </div>
</template>

<script setup>
import StatCard from '../components/StatCard.vue';

import { ref } from 'vue';

import { Configuration, OpenAIApi } from "openai";

const configuration = new Configuration({
    organization: import.meta.env.VITE_OPENAI_ORGANIZATION,
    apiKey: import.meta.env.VITE_OPENAI_API_KEY,
});


const openai = new OpenAIApi(configuration);

const isStat = ref(false);
const statReason = ref("");

const promptFlag = ref(false);
const loading = ref(false);

const prompt = ref("");
const promptSetup = "I will give a patient condition. Respond only with the a body of json and provide value of true for a key of possibleStat if the scenario described most likely may  require urgent intervention and false if they do not require urgent intervention. Also provide your reasoning in less than 100 words as a value for key of reason. Here's the condition -"


async function submitPrompt() {
  loading.value = true;
  promptFlag.value = false;
  let response = await openai.createChatCompletion({
    model: 'gpt-3.5-turbo',
    messages: [
        {
            "role": "user",
            "content": `${promptSetup} ${prompt.value}`
        }
    ],
  })

  if(response.status === 200){
    let statResponse = extractJSONfromResponse(response.data)
    isStat.value = statResponse.possibleStat;
    statReason.value = statResponse.reason;
    promptFlag.value = true;
  } else {
    console.error(response);
  }

  loading.value = false;
}

// function testPrompt(){
//   let testResponse = {"id":"chatcmpl-7HviIgS2fC8IYwCmyIB7scBu8mrTC","object":"chat.completion","created":1684507782,"model":"gpt-3.5-turbo-0301","usage":{"prompt_tokens":91,"completion_tokens":91,"total_tokens":182},"choices":[{"message":{"role":"assistant","content":"{\n  \"possibleStat\": true,\n  \"reason\": \"A hip fracture in an elderly patient can lead to severe pain, immobility, and increased risk of complications such as blood clots, pneumonia, and bedsores. Urgent medical attention is necessary to stabilize the fracture and provide pain relief. Depending on the severity of the fracture, surgical intervention may be required as soon as possible to improve outcomes and reduce risk of adverse events.\"\n}"},"finish_reason":"stop","index":0}]}
//   let test = extractJSONfromResponse(testResponse)
//   isStat.value = test.possibleStat;
//   statReason.value = test.reason;
// }

// Extract json from prompt response. 
function extractJSONfromResponse(promptResponse){
  return JSON.parse(promptResponse.choices[0].message.content);
}

</script>


<style scoped>

.stat-card {
  margin-top: 40px;
}

.container {
  width: 100%;
  margin-left: 300px;
}
</style>
