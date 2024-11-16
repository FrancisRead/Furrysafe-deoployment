<template>
    <section class="bg-white">
        <div class="lg:grid min-h-screen lg:grid-cols-12">
            <aside class="bg-blue-50 w-full right-[6%] relative lg:order-last lg:col-span-5 lg:h-full hidden lg:block ">
                <!-- Check if this image is causing issues -->
                <img alt="" :src="require('@/assets/images/home_animalshelter_slider_pic4.png')"
                    class="absolute inset-0 my-20 mx-16 h-[90%] w-[90%] object-contain " />
            </aside>

            <main
                class="flex items-center justify-center min-h-screen px-8 py-8 sm:px-12 lg:col-span-7 lg:px-16 lg:py-10 xl:col-span-6">
                <div class=" text-left w-full md:w-2/3 pt-10 pb-10 md:pt-2 mx-auto">
                    <form @submit.prevent="isSignUp ? handleSignUpSubmit : handleLoginSubmit">
                        <div class="mb-6" v-if="!isSignUp">
                            <h2 class="text-4xl font-semibold mb-4 sm:text-center lg:text-left"> Welcome Back</h2>
                            <p class="text-sm text-gray-500 mb-8 pt-2 pb-4 sm:text-center lg:text-left">
                                We're glad to see you again! Ready to continue making a difference for animals today?
                            </p>
                            <label for="email" class="text-gray-800 text-sm mb-2 block font-semibold">Email</label>
                            <input type="text" id="email" v-model="loginEmail"
                                class="w-full text-sm border border-gray-200 bg-white px-4 py-3 rounded-md text-gray-700 shadow-sm"
                                placeholder="Example@mail.com" />
                        </div>
                        <div class="pb-3" v-if="!isSignUp">
                            <passwordunhide />
                        </div>

                        <div class="flex items-center justify-between mb-6" v-if="!isSignUp">
                            <div class="flex items-center">
                                <input id="rememberMe" name="rememberMe" type="checkbox"
                                    class="h-4 w-4 text-indigo-600 border-gray-300 rounded" />
                                <label for="rememberMe" class="ml-2 block text-sm text-gray-900"> Keep me signed in
                                </label>
                            </div>
                            <div class="text-sm">
                                <a @click="navigateTo('/forgot-password')" class="cursor-pointer">
                                    <u>Forget password</u>
                                </a>
                            </div>
                        </div>
                        <button @click.prevent="handleLogin()" type="submit"
                            class="font-semibold w-full text-white bg-gray-800 hover:bg-gray-700 py-2 px-4 rounded-md hover:bg-darkblue focus:outline-none focus:ring-2"
                            v-if="!isSignUp">
                            Sign in
                        </button>
                        <div v-if="isSignUp">
                            <h3 class="text-2xl text-center font-semibold mb-4">How are you planning to Register?</h3>
                            <p class="text-sm pb-10 text-center text-gray-600 break-words">
                                Register as a Buddy, helping animals, fostering, or reporting; or as a Shelter, managing
                                adoptions and animal care.
                            </p>
                            <div class="flex justify-between sm:h-32 md:h-48">
                                <button type="button"
                                    class="border rounded-lg p-4 w-full mr-2 bg-gray-50 hover:bg-lightteal hover:text-white"
                                    @click="goToRegis">
                                    <div class="font-semibold">Buddy</div>
                                </button>
                                <button type="button"
                                    class="border rounded-lg p-4 w-full ml-2 bg-gray-50 hover:bg-lightorange hover:text-white"
                                    @click="goToRegis2">
                                    <div class="font-semibold">Shelter</div>
                                </button>
                            </div>
                        </div>
                        <div class="mt-6 text-center">

                            <p class="text-sm text-gray-600">
                                {{ isSignUp ? 'Already have an account?' : 'Don\'t have an account?' }}
                                <button @click="toggleForm" class="font-medium text-teal-600 hover:text-teal-500">{{
                                    isSignUp ?
                                        'Sign in' : 'Sign up' }}</button>
                            </p>
                        </div>
                    </form>
                </div>
            </main>
        </div>
    </section>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from "axios";
import passwordunhide from "../components/passwordHide.vue";

const API_BASE_URL = 'https://capstone-furrysafe-deployment.onrender.com';
const router = useRouter();

console.log("login function:)");

// Icons or images
const dog = require('@/assets/images/animalshelterdog.png');

// State variables
const loginEmail = ref('');
const loginPassword = ref('');
const signupPassword = ref('');
const isSignUp = ref(false);
const usageType = ref('');
const usageType2 = ref('');
const passwordError = ref(false);
const showPassword = ref(false);
const userEmail = ref('');
const userPassword = ref('');
const items = ref([]);
const loginError = ref(''); // Store login error messages

// Methods
const toggleForm = () => {
    isSignUp.value = !isSignUp.value;
};

const navigateTo = (path) => {
    router.push(path);
};

const handleLogin = async () => {
    try {
        await getUser();
    } catch (err) {
        console.log(err);
    }
};

const getUser = async () => {
    try {
        console.log("login")
        const response = await axios.post(`${API_BASE_URL}/login`, {
            email: userEmail.value,
            password: userPassword.value
        }, {
            withCredentials: true // Include cookies if needed
        });

        items.value = response.data;
        console.log("login", response.data);

        // Save tokens and user data to localStorage
        localStorage.setItem("access_token", items.value.token);
        localStorage.setItem("u_type", items.value.userType);
        localStorage.setItem("u_id", items.value.userID);
        localStorage.setItem("c_id", items.value.characterId);
        localStorage.setItem("address_exists", items.value.address_exists);

        // Redirect based on user type
        if (response.data.success) {
            const userType = response.data.userType;
            if (userType === 'shelter') {
                navigateTo('/shelterDashboard');
            } else if (userType === 'buddy') {
                navigateTo('/buddydashboard');
            } else if (userType === 'admin') {
                navigateTo('/dashboard');
            }
        } else {
            loginError.value = response.data.message || "Invalid login credentials";
            if (response.status === 403 && response.data.message === 'Shelter is not verified') {
                console.log('Shelter is not verified. Please verify your shelter documents.');
            }
        }

    } catch (err) {
        if (err.response) {
            console.log("Error Response Data:", err.response.data);
            console.log("Status Code:", err.response.status);
            loginError.value = err.response.data.message || "Login failed due to server error";
        } else {
            console.log("An ERROR occurred:", err.message);
            loginError.value = "An unexpected error occurred";
        }
    }
};

const handleSignUpSubmit = () => {
    // Placeholder for sign-up logic
    console.log("Sign up submitted");
};

const goToRegis = () => {
    navigateTo("/buddy_registration");
};

const goToRegis2 = () => {
    navigateTo("/shelter_registration");
};
</script>

<style scoped>
/* Add any component-specific styles here */
/* Consider adjusting margins or padding if needed */
.bg-blue-200 {
    background-color: #bfdbfe;
    /* Light blue color for highlighting */
}
</style>