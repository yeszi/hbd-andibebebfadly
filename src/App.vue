<template>
  <div class="min-h-screen bg-[#FDF8F5] flex items-center justify-center p-4 relative overflow-hidden" @click="enableAudioOnce">
    
    <div class="absolute inset-0 overflow-hidden">
      <div class="absolute -top-20 -left-20 w-96 h-96 bg-[#F5E8E1] rounded-full mix-blend-multiply filter blur-3xl opacity-50"></div>
      <div class="absolute -bottom-20 -right-20 w-96 h-96 bg-[#FFE4E1] rounded-full mix-blend-multiply filter blur-3xl opacity-50"></div>
    </div>

    <div v-if="!showGift && !showQuiz" class="relative z-10 max-w-sm w-full">
      <div class="bg-white/80 backdrop-blur-sm rounded-[2.5rem] p-10 shadow-lg text-center border border-[#E8D5C4] transition-all duration-500">
        
        <div class="mb-6">
          <div class="text-7xl mb-3 inline-block">
                 Andikz a.k.a Mbak Dep 🎀 
		 </div>
        </div>
        
        <h1 class="text-3xl font-serif text-[#8D7B68] mb-1 tracking-tight"> I Wanna Gift Something For You </h1>
        <h2 class="text-2xl font-medium text-[#A4907C] mb-4"></h2>
        
        <p class="text-[#BC8F8F] text-sm mb-8 leading-relaxed font-light">
          klik tombol dibawah!
        </p>
        
        <button 
          @click="startQuiz"
          class="group relative bg-[#DDB9A3] hover:bg-[#C9A690] text-white font-medium py-3 px-10 rounded-full shadow-sm transition-all duration-300 active:scale-95"
        >
          <span class="flex items-center gap-2 justify-center">
            
            <span class="text-lg group-hover:rotate-12 transition-transform">> 🎁</span>
          </span>
        </button>
      </div>
    </div>

    <div v-if="showQuiz && !showGift" class="relative z-10 max-w-md w-full">
      <div class="bg-white/90 backdrop-blur-sm rounded-[2.5rem] p-8 shadow-xl border border-[#F1DEC9] text-center">
        <div class="mb-4">
          <span class="text-5xl">🔒</span>
        </div>
        
        <h3 class="text-xl font-medium text-[#8D7B68] mb-3">Etss Tunggu duluu...</h3>
        <p class="text-[#A4907C] text-sm mb-6 leading-relaxed">{{ quizQuestion }}</p>
        
        <div class="space-y-4 mb-6">
          <input 
            v-model="quizAnswer" 
            type="text" 
            placeholder="Jawab di sini..."
            class="w-full px-5 py-3 rounded-2xl border border-[#F1DEC9] bg-[#FFFBF8] text-[#8D7B68] focus:outline-none focus:ring-2 focus:ring-[#DDB9A3] text-center transition-all"
            @keyup.enter="checkAnswer"
          />
          
          <div class="flex gap-3 justify-center">
            <button 
              @click="checkAnswer"
              class="bg-[#DDB9A3] hover:bg-[#C9A690] text-white px-8 py-2.5 rounded-full transition shadow-sm"
            >
              kirim 
            </button>
            <button 
              @click="cancelQuiz"
              class="bg-[#F5E8E1] hover:bg-[#E8D5C4] text-[#8D7B68] px-8 py-2.5 rounded-full transition"
            >
             <
            </button>
          </div>
        </div>
        
        <div v-if="quizMessage" class="text-sm mt-3 font-medium" :class="quizMessageType === 'error' ? 'text-red-400' : 'text-[#8D7B68]'">
          {{ quizMessage }}
        </div>
      </div>
    </div>

    <div v-else-if="showGift" class="relative z-10 max-w-md w-full">
      <div class="bg-white rounded-[2.5rem] p-8 shadow-2xl border border-[#E8D5C4] relative">
        <div class="text-center">
          <div class="mb-6">
            <div class="w-32 h-32 mx-auto bg-[#FDF1E6] rounded-full flex items-center justify-center border-4 border-white shadow-sm">
              <span class="text-6xl">✨</span>
            </div>
          </div>
          
          <h3 class="text-2xl font-serif text-[#8D7B68] mb-1">Special for You,</h3>
          <h4 class="text-xl font-medium text-[#A4907C] mb-6">Andibebebfadly 🕊️</h4>
          
          <div class="bg-[#FCF8F5] rounded-3xl p-6 text-left mb-6 border border-[#F1DEC9]">
            <p class="text-[#8D7B68] text-sm leading-loose italic">
              "Happyyy birthday Andikssz! 🤎<br><br>
              Semoga panjang umur dan sehat selalu, juga hari-harimu selalu diwarnai kebahagiaan, 
              dilimpahi cinta, dan dikelilingi orang-orang baik. 
              Tetap jadi pribadi yang taat kepada orang tua dan yang Maha Kuasa dan apa adanya ya ndik.<br><br>
		Maaf belum bisa kasih apa-apa
              Semoga di hari ulangtahun mu yang ke 22 ini segala keinginan dan rencana hidupmu dikabulkan tanpa hambatan, 
              lancar luncur tugas akhir, dan dapat mencapai gelar itu di hari yang kau rancangkan ya ndik. 
              Tetap jadi temanku yang comel ya ndik, walaupun beberapa kebelakangan ini kita jarang bermain, i hope u still healthy.<br><br>
              Love you lots! 🌸✨"
            </p>
          </div>

          <button 
            @click="closeGift" 
            class="text-xs text-[#BC8F8F] hover:text-[#8D7B68] transition-colors bg-[#FDF1E6] px-6 py-2 rounded-full"
          >
            Tutup Pesan
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import confetti from 'canvas-confetti'

const showGift = ref(false)
const showQuiz = ref(false)
const quizAnswer = ref('')
const quizMessage = ref('')
const quizMessageType = ref('')

let audio = null
let audioEnabled = false

const quizQuestion = "warna kesukaan aku apa?"
const correctAnswer = "ungu"

// Piano Version Link
const MUSIC_URL = 'https://cdn.pixabay.com/audio/2022/11/14/audio_7302484931.mp3'

const initAudio = () => {
  if (!audio) {
    audio = new Audio(MUSIC_URL)
    audio.loop = true
    audio.volume = 0.4
  }
}

const enableAudioOnce = () => {
  if (!audioEnabled && audio) {
    audio.play().then(() => {
      audioEnabled = true
    }).catch(err => console.log('Autoplay blocked:', err))
  }
}

const startQuiz = () => {
  enableAudioOnce()
  showQuiz.value = true
}

const checkAnswer = () => {
  if (quizAnswer.value.trim().toLowerCase() === correctAnswer) {
    quizMessage.value = 'Yeay! Kamu ingat! 🤎✨'
    quizMessageType.value = 'success'
    showQuiz.value = false
    showGift.value = true
    
    // Confetti dengan warna yang senada (Coklat, Cream, Pink)
    confetti({
      particleCount: 100,
      spread: 70,
      origin: { y: 0.6 },
      colors: ['#DDB9A3', '#E8D5C4', '#F1DEC9', '#FFD1D1']
    })
  } else {
    quizMessage.value = 'Yah salah... kita ga dekat lagi ya ndik? 🥺'
    quizMessageType.value = 'error'
    quizAnswer.value = ''
  }
}

const cancelQuiz = () => {
  showQuiz.value = false
}

const closeGift = () => {
  showGift.value = false
  showQuiz.value = false
}

onMounted(() => initAudio())
onUnmounted(() => {
  if (audio) {
    audio.pause()
    audio = null
  }
})
</script>

<style scoped>
/* Hanya animasi dasar agar tidak pusing */
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-4px); }
  75% { transform: translateX(4px); }
}

input:focus {
  transform: translateY(-2px);
}
</style>
