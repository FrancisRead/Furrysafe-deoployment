<script setup>
import { ref, watch} from 'vue';

const emit = defineEmits();

const password = ref('');
const passwordType = ref('password');

const eye = 'https://img.icons8.com/external-flatart-icons-outline-flatarticons/64/4D4D4D/external-eye-basic-ui-elements-flatart-icons-outline-flatarticons.png';
const eyeHide = 'https://img.icons8.com/external-flatart-icons-outline-flatarticons/64/4D4D4D/external-eye-devices-flatart-icons-outline-flatart-icons.png';

const togglePasswordVisibility = () => {
  passwordType.value = passwordType.value === 'password' ? 'text' : 'password';
};

watch(() => {
  if (password.value) {
    // console.log("password here" , password.value)
    emit('password', password.value)
  }
});
</script>

<template>
  <div>
    <div>
      <label class="text-gray-800 text-sm mb-2 block font-semibold" for="email">Password </label>
    </div>
    <div class="relative">
      <input :type="passwordType" id="password" placeholder="Password"
        class="w-full text-sm border border-gray-200 bg-white px-4 py-3 rounded-md text-gray-700 shadow-sm"
        v-model="password" />
      <span class="absolute top-1/2 right-5 transform -translate-y-1/2">
        <!-- to hide icon-->
        <img v-if="passwordType === 'password'" :src="eyeHide" alt="Unsee Eye Icon"
          class="sm:w-4 sm:h-4 lg:w-5 lg:h-5 cursor-pointer" @click="togglePasswordVisibility" />
        <!-- to see icon-->
        <img v-else :src="eye" alt="Eye Icon" class="sm:w-4 sm:h-4 lg:w-5 lg:h-5 cursor-pointer"
          @click="togglePasswordVisibility" />
      </span>
    </div>
  </div>
</template>