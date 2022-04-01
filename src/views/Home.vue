<template>
            <form @submit.prevent="generatelink">
            <div class="w-[90vw] md:w-[400px] py-7 rounded-xl shadow-xl bg-white flex flex-col justify-center items-center font-pupylinux">
                <p class="font-medium mb-5 text-2xl">Url Shortener</p>
                <input type="text" v-model="form.url" placeholder="Url" class="border rounded-lg py-2 px-3 w-[60vw] md:w-[250px]">
                <input type="text" v-model="form.slashtag" placeholder="Slashtag" class="border rounded-lg py-2 px-3 mt-4 w-[60vw] md:w-[250px] mb-4">
                <button v-if="loadingstate" type="submit" class="py-3 px-11 font-normal bg-slate-400 text-white rounded-lg">
                    <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="margin: auto; background: none; display: block; shape-rendering: auto;" width="25px" height="25px" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid">
<circle cx="50" cy="50" fill="none" stroke="#ffffff" stroke-width="10" r="35" stroke-dasharray="164.93361431346415 56.97787143782138">
  <animateTransform attributeName="transform" type="rotate" repeatCount="indefinite" dur="1s" values="0 50 50;360 50 50" keyTimes="0;1"></animateTransform>
</circle></svg>
                </button>
                <button v-else type="submit" class="py-3 px-5 font-normal bg-slate-400 text-white rounded-lg">Generate</button>
                <div v-if="hasil" class="bg-slate-300 flex items-center mt-6 px-4 rounded-lg">
                <p class="py-3 rounded-md w-[250px] relative text-blue-400">{{ hasilurl }}</p>
                <img :src="imgcopy" @click="copyurl" class="w-[20px] cursor-pointer" alt="">
                </div>
            </div>
            </form>

            <transition name="invalid">
            <div v-if="invalid" class="absolute py-5 px-7 top-10 rounded-xl left-0 right-0 bg-red-500 w-max font-pupylinux mx-auto text-white">
                <p>Slashtag Sudah Digunakan!!</p>
            </div>
            </transition>
</template>

<script>
import axios from 'axios'
import { ref } from '@vue/reactivity'
export default {
    setup() {
        const form = ref({
            url: '',
            slashtag: ''
        })
        const hasil = ref(false)
        const hasilurl = ref('')
        const imgcopy = ref('/img/copy.png')
        
        const loadingstate = ref(false)

        const invalid = ref(false)

        const generatelink = async () => {
            loadingstate.value = true
            try {
                const {data} = await axios.get(`https://api.rebrandly.com/v1/links/new?apikey=7e21e5a7214b45ca967b600037e4a145&destination=${form.value.url}&slashtag=${form.value.slashtag}&domain[fullName]=rebrand.ly`)
                hasilurl.value = data.shortUrl
                hasil.value = true
                loadingstate.value = false
            } catch (error) {
                invalid.value = true
                setTimeout(() => {
                    invalid.value = false
                    loadingstate.value = false
                }, 1000);
            }
        }

        const copyurl = () => {
            navigator.clipboard.writeText(hasilurl.value)
            imgcopy.value = '/img/checked.png'
        }

        return { generatelink , form  , hasil , hasilurl , imgcopy , copyurl , invalid , loadingstate}
    }
}
</script>
<style>
.invalid-enter-from {
    opacity: 0;
    transform: translateY(-50px);
}
.invalid-enter-to{
    opacity: 1;
}
.invalid-enter-active{
    transition: all .2s ease;
}

.invalid-leave-to {
    opacity: 0;
    transform: translateY(-50px);
}
.invalid-leave-active{
    transition: all .3s;
}
</style>