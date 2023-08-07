<script setup>
import { ref, computed, onMounted } from 'vue'

const wins = ref(0)
const draws = ref(0)
const losses = ref(0)

const choice = ref(null)
const computerChoice = ref(null)
const verdict = ref(null)

const outcomes = {
  Taş: {
    Taş: 'draw',
    Kağıt: 'loss',
    Makas: 'win',
  },
  Kağıt: {
    Taş: 'win',
    Kağıt: 'draw',
    Makas: 'loss',
  },
  Makas: {
    Taş: 'loss',
    Kağıt: 'win',
    Makas: 'draw',
  },
}

const winPercentage = computed(() => {
  const total = wins.value + draws.value + losses.value
  return total ? (wins.value / total) * 100 : 0
})

const play = c => {
  choice.value = c

  const choices = ['Taş', 'Kağıt', 'Makas']
  const random = Math.floor(Math.random() * choices.length)
  computerChoice.value = choices[random]

  const outcome = outcomes[c][computerChoice.value]

  if (outcome === 'win') {
    wins.value++
    verdict.value = 'Kazandın !'
  } else if (outcome === 'loss') {
    losses.value++
    verdict.value = 'Kaybettin !'
  } else {
    draws.value++
    verdict.value = 'Berabere !'
  }

  SaveGame()
}

const SaveGame = () => {
  localStorage.setItem('wins', wins.value)
  localStorage.setItem('draws', draws.value)
  localStorage.setItem('losses', losses.value)
}

const LoadGame = () => {
  wins.value = Number(localStorage.getItem('wins')) || 0
  draws.value = Number(localStorage.getItem('draws')) || 0
  losses.value = Number(localStorage.getItem('losses')) || 0
}

const ResetRound = () => {
  choice.value = null
  computerChoice.value = null
  verdict.value = null
}

const ResetGame = () => {
  wins.value = 0
  draws.value = 0
  losses.value = 0
  ResetRound()
}

onMounted(() => {
  LoadGame()
  window.addEventListener('keypress', e => {
    if (e.key === 'r') {
      ResetRound()
    }
  })
})

const getVerdictClass = computed(() => {
  if (verdict.value === 'Kazandın !') {
    return 'text-green-900';
  } else if (verdict.value === 'Kaybettin !') {
    return 'text-red-700';
  } else if (verdict.value === 'Berabere !') {
    return 'text-white';
  } else {
    return '';
  }
})

</script>

<template>
  <div class="bg-gradient-to-r from-green-500 to-green-700 text-white text-center min-h-screen flex flex-col">
    <header class="container mx-auto p-6">
      <div class="flex justify-center mb-10">
        <img src="/src/assets/rock-paper-scissors.png"
          class="w-40 h-40 bg-transparent rounded-full border-4 border-white" />
      </div>
      <h1 class="text-4xl font-bold w-full cursor-default">TAŞ KAĞIT MAKAS</h1>
    </header>

    <main class="container mx-auto p-6 flex-1">
      <div v-if="choice === null" class="flex items-center justify-center -mx-6">
        <button @click="play('Taş')"
          class="bg-white rounded-full shadow-lg w-64 p-4 mx-6 transition-colors duration-300 hover:bg-gray-300 md:p-12">
          <img src="/src/assets/fist.png" alt="Rock" class="w-full" />
        </button>
        <button @click="play('Kağıt')"
          class="bg-white rounded-full shadow-lg w-64 p-4 mx-6 transition-colors duration-300 hover:bg-gray-300 md:p-12">
          <img src="/src/assets/paper.png" alt="Paper" class="w-full" />
        </button>
        <button @click="play('Makas')"
          class="bg-white rounded-full shadow-lg w-64 p-4 mx-6 transition-colors duration-300 hover:bg-gray-300 md:p-12">
          <img src="/src/assets/scissor.png" alt="Scissors" class="w-full" />
        </button>
      </div>

      <div v-else>
        <div class="text-3xl mb-4">
          Sen : <span class="text-white">{{ choice }}</span>
        </div>
        <div class="text-3xl mb-4">
          Bilgisayar : <span class="text-white">{{ computerChoice }}</span>
        </div>
        <div :class="getVerdictClass" class="text-4xl mb-12">
          {{ verdict }}
        </div>
        <button @click="ResetRound"
          class="bg-white text-gray-600 text-lg py-2 px-4 rounded-md border-2 border-gray-400 mr-5">
          Tekrar Oyna
        </button>
        <button @click="ResetGame"
          class="bg-white text-gray-600 text-lg py-2 px-4 rounded-md border-2 border-gray-400 ml-5">
          Yeni Oyun
        </button>
      </div>

      <div class="flex justify-center mt-10 mb-10">
        <table class="border-separate border-4 border-white rounded-lg">
          <tbody>
            <tr>
              <td class="border-4 border-white p-4">Kazandın:</td>
              <td class="border-4 border-white p-4">{{ wins }}</td>
              <td class="border-4 border-white p-4">Kaybettin:</td>
              <td class="border-4 border-white p-4">{{ losses }}</td>
            </tr>
            <tr>
              <td class="border-4 border-white p-4">Berabere:</td>
              <td class="border-4 border-white p-4">{{ draws }}</td>
              <td class="border-4 border-white p-4">Kazanma Oranı:</td>
              <td class="border-4 border-white p-4">{{ Math.round(winPercentage) }}%</td>
            </tr>
          </tbody>
        </table>
      </div>

    </main>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600&display=swap");

* {
  margin: 0;
  padding: 0;
}

body {
  font-family: "Poppins", sans-serif;
}

html {
  scroll-behavior: smooth;
}

p {
  color: rgb(85, 85, 85);
}
</style>