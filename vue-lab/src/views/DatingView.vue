<script setup lang="ts">
import { ref, reactive, computed } from 'vue';

// --- СТАН (STATE) ---

// Логіка Onboarding
const currentSlide = ref(0);
const showAuth = ref(false);

const slides = [
  { img: '/img1.png', text: 'Знаходь нових друзів та спілкуйся без меж!' },
  { img: '/img2.png', text: 'Створюй пари за інтересами та хобі.' },
  { img: '/img3.png', text: 'Твоя ідеальна половинка вже чекає на тебе!' }
];

// Логіка Авторизації
const authMode = ref<'login' | 'register'>('register'); // 'login' або 'register'

// Дані форми (Reactive об'єкт)
const form = reactive({
  username: '',
  password: '',
  email: ''
});

// Помилки валідації
const errors = reactive({
  username: '',
  password: '',
  email: ''
});

// --- МЕТОДИ (FUNCTIONS) ---

// Перемикання слайдів
const nextSlide = () => {
  if (currentSlide.value < slides.length - 1) {
    currentSlide.value++;
  } else {
    finishOnboarding();
  }
};

const finishOnboarding = () => {
  showAuth.value = true;
};

// Перемикання табів (Вхід / Реєстрація)
const switchTab = (mode: 'login' | 'register') => {
  authMode.value = mode;
  // Очищаємо помилки при перемиканні
  errors.username = '';
  errors.password = '';
  errors.email = '';
};

// ВАЛІДАЦІЯ ТА ВІДПРАВКА
const submitForm = () => {
  // 1. Скидаємо старі помилки
  errors.username = '';
  errors.password = '';
  errors.email = '';
  let isValid = true;

  // 2. Перевірка Username
  if (!form.username) {
    errors.username = 'Введіть ім\'я користувача';
    isValid = false;
  } else if (form.username.length < 3) {
    errors.username = 'Ім\'я має бути не менше 3 символів';
    isValid = false;
  }

  // 3. Перевірка Password
  if (!form.password) {
    errors.password = 'Введіть пароль';
    isValid = false;
  } else if (form.password.length < 6) {
    errors.password = 'Пароль має бути не менше 6 символів';
    isValid = false;
  }

  // 4. Перевірка Email (тільки для реєстрації)
  if (authMode.value === 'register') {
    if (!form.email) {
      errors.email = 'Введіть Email';
      isValid = false;
    } else if (!form.email.includes('@')) {
      errors.email = 'Некоректний формат Email';
      isValid = false;
    }
  }

  // 5. Якщо все добре — виводимо в консоль
  if (isValid) {
    console.log(' Успішна відправка форми');
    console.log('Тип:', authMode.value === 'login' ? 'Вхід' : 'Реєстрація');
    console.log('Дані:', { ...form }); // Копіюємо об'єкт для красивого виводу
    
    alert(`Дані відправлено в консоль! \nКористувач: ${form.username}`);
  } else {
    console.warn(' Форма містить помилки');
  }
};
</script>

<template>
  <div class="min-h-[80vh] flex justify-center items-center bg-gray-200 py-10 font-sans">
    
    <!-- РАМКА ТЕЛЕФОНУ -->
    <div class="relative w-[375px] h-[750px] bg-white shadow-2xl rounded-[30px] overflow-hidden flex flex-col">

      <!-- === ЧАСТИНА 1: ONBOARDING === -->
      <!-- Показуємо цей блок, якщо showAuth === false -->
      <div v-if="!showAuth" class="w-full h-full flex flex-col transition-all duration-500">
        
        <!-- Слайд (Динамічний) -->
        <div class="flex-1 flex flex-col items-center justify-start pt-[60px] px-6">
          <div class="w-full h-[40%] flex justify-center items-end mb-8 transition-opacity duration-300">
            <!-- Картинка змінюється залежно від currentSlide -->
            <img :src="slides[currentSlide].img" alt="Slide" class="max-w-[85%] max-h-full object-contain drop-shadow-lg">
          </div>

          <button @click="finishOnboarding" class="bg-[#FF5656] text-white px-6 py-3.5 text-base font-extrabold uppercase tracking-widest shadow-lg shadow-red-500/30 mb-6 rounded hover:scale-105 active:scale-95 transition-transform">
            Meet New People
          </button>
          
          <p class="text-center text-sm font-bold text-gray-600 leading-relaxed px-4">
            {{ slides[currentSlide].text }}
          </p>
        </div>

        <!-- Навігація знизу -->
        <div class="h-[80px] px-8 flex items-center justify-between bg-white z-10">
          <button @click="finishOnboarding" class="bg-gray-100 text-gray-500 px-5 py-2 text-[10px] font-extrabold tracking-widest rounded hover:bg-gray-200 transition-colors">
            SKIP
          </button>
          
          <!-- Крапки -->
          <div class="flex gap-2">
            <div 
              v-for="(slide, index) in slides" 
              :key="index"
              class="w-2.5 h-2.5 rounded-sm transition-colors duration-300"
              :class="currentSlide === index ? 'bg-[#FF5656]' : 'bg-gray-200'"
            ></div>
          </div>

          <button @click="nextSlide" class="bg-[#FF5656] text-white px-5 py-2 text-[10px] font-extrabold tracking-widest rounded shadow-md hover:bg-red-600 transition-colors">
            NEXT
          </button>
        </div>
      </div>

      <!-- === ЧАСТИНА 2: АВТОРИЗАЦІЯ === -->
      <!-- Показуємо, якщо showAuth === true -->
      <div v-else class="w-full h-full bg-[#FF5656] flex flex-col items-center px-8 pt-10 animate-fade-in">
        
        <!-- Лого -->
        <div class="w-[100px] h-[100px] bg-white rounded-full flex justify-center items-center mb-6 mt-4 shadow-lg">
          <img src="https://cdn-icons-png.flaticon.com/512/1077/1077035.png" alt="Heart" class="w-[60%] opacity-90">
        </div>
        
        <h1 class="text-3xl font-black text-white tracking-widest mb-8 uppercase">FATED</h1>

        <!-- Таби -->
        <div class="flex gap-4 mb-6 w-full">
          <button 
            @click="switchTab('login')" 
            class="flex-1 py-3 rounded-lg font-bold text-xs uppercase tracking-widest transition-all"
            :class="authMode === 'login' ? 'bg-white text-[#FF5656]' : 'bg-white/20 text-white hover:bg-white/30'"
          >
            Login
          </button>
          <button 
            @click="switchTab('register')" 
            class="flex-1 py-3 rounded-lg font-bold text-xs uppercase tracking-widest transition-all"
            :class="authMode === 'register' ? 'bg-white text-[#FF5656]' : 'bg-white/20 text-white hover:bg-white/30'"
          >
            Register
          </button>
        </div>

        <!-- Поля вводу (Форма) -->
        <div class="w-full flex flex-col gap-4 mb-2">
          
          <!-- Username -->
          <div>
            <input 
              v-model="form.username" 
              type="text" 
              placeholder="username" 
              class="w-full p-4 rounded-lg outline-none text-sm font-bold text-gray-800 text-center placeholder:text-gray-400"
              :class="errors.username ? 'border-2 border-yellow-300' : 'border-none'"
            >
            <p v-if="errors.username" class="text-white text-[10px] font-bold mt-1 text-center">{{ errors.username }}</p>
          </div>

          <!-- Password -->
          <div>
            <input 
              v-model="form.password" 
              type="password" 
              placeholder="password" 
              class="w-full p-4 rounded-lg outline-none text-sm font-bold text-gray-800 text-center placeholder:text-gray-400"
              :class="errors.password ? 'border-2 border-yellow-300' : 'border-none'"
            >
            <p v-if="errors.password" class="text-white text-[10px] font-bold mt-1 text-center">{{ errors.password }}</p>
          </div>

          <!-- Email (показуємо тільки при реєстрації) -->
          <div v-if="authMode === 'register'">
            <input 
              v-model="form.email" 
              type="email" 
              placeholder="email" 
              class="w-full p-4 rounded-lg outline-none text-sm font-bold text-gray-800 text-center placeholder:text-gray-400"
              :class="errors.email ? 'border-2 border-yellow-300' : 'border-none'"
            >
            <p v-if="errors.email" class="text-white text-[10px] font-bold mt-1 text-center">{{ errors.email }}</p>
          </div>
        </div>

        <a href="#" class="text-white/80 text-[10px] font-bold self-end mb-8 uppercase hover:text-white mt-2">Forgot Password?</a>

        <!-- Кнопка відправки -->
        <button 
          @click="submitForm" 
          class="w-full py-4 bg-white text-[#FF5656] rounded-lg text-sm font-black uppercase tracking-widest shadow-xl hover:bg-gray-50 active:scale-95 transition-all mb-5"
        >
          {{ authMode === 'login' ? 'LOGIN' : 'REGISTER' }}
        </button>

        <div class="text-[10px] text-white font-bold uppercase mt-auto mb-6 cursor-pointer" @click="switchTab(authMode === 'login' ? 'register' : 'login')">
          <span class="opacity-70">
            {{ authMode === 'login' ? "Don't have an account?" : "Already have an account?" }}
          </span> 
          {{ authMode === 'login' ? 'Register' : 'Login' }}
        </div>

      </div>

    </div>
  </div>
</template>

<style>
/* Додаткова анімація появи */
@keyframes fade-in {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
.animate-fade-in {
  animation: fade-in 0.4s ease-out forwards;
}
</style>