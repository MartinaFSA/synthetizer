<script setup>
import TheWelcome from './components/TheWelcome.vue'
import { initializeApp } from "firebase/app";
const firebaseConfig = {
  apiKey: "AIzaSyCA1VqzliAe4H_JV7mWyub0GfkziB-kd6s",
  authDomain: "synthetizer-9dd5e.firebaseapp.com",
  projectId: "synthetizer-9dd5e",
  storageBucket: "synthetizer-9dd5e.appspot.com",
  messagingSenderId: "162139151784",
  appId: "1:162139151784:web:9bc3cd007f64614104119d"
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