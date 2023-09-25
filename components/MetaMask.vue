<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';

const account = ref<string | null>(null);
const error = ref<string | null>(null);

const connectMetaMask = async () => {
  try {
    if (typeof window.ethereum !== 'undefined') {
      const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
      account.value = accounts[0];
    } else {
      error.value = 'MetaMaskがインストールされていません。';
    }
  } catch (err) {
    console.error('MetaMask接続エラー:', err);
    error.value = err.message;
  }
};

const login = async () => {
  try {
    const message = "Please sign this message to log in.";
    const signature = await window.ethereum.request({
      method: 'personal_sign',
      params: [message, account.value],
    });

    const response = await axios.post('http://127.0.0.1:8000/login', {
      account: account.value,
      signature,
    });

    console.log('Login response:', response.data);
  } catch (err) {
    console.error('Login error:', err);
    error.value = err.message;
  }
};
</script>

<template>
    <div>
        <v-btn v-if="!account" color="primary" @click="connectMetaMask">MetaMaskに接続</v-btn>
        <v-btn v-if="account" color="primary" @click="login">ログイン</v-btn>
        <div v-if="error" class="error">{{ error }}</div>
    </div>
</template>

<style lang="scss" scoped>
.error {
  color: red;
}
</style>
