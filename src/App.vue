<script setup>
import TheWelcome from './components/TheWelcome.vue'
import { initializeApp } from "firebase/app";
const firebaseConfig = {
  apiKey: import.meta.env.VUE_APIKEY,
  authDomain: import.meta.env.VUE_AUTHDOMAIN,
  projectId: import.meta.env.VUE_PROJECTID,
  storageBucket: import.meta.env.VUE_STORAGEBUCKET,
  messagingSenderId: import.meta.env.VUE_MESSAGESENDERID,
  appId: import.meta.env.VUE_APPID
};

const app = initializeApp(firebaseConfig);
</script>

<template>
  <header>
    <!--nav-->
  </header>

  <main>
    <TheWelcome />
  </main>
</template>