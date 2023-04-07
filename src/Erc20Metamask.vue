<template>
    <div class="erc20-metamask">
      <h1>Connect to MetaMask and Access ERC20 Token Contract</h1>
      <button v-if="!isConnected" @click="connectMetaMask">Connect to MetaMask</button>
      <div v-if="isConnected">
        <p>Connected Address: {{ connectedAddress }}</p>
        <button @click="fetchBalance">Get Balance</button>
        <div v-if="balance !== null">
          <p>Balance: {{ balance }}</p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { ethers } from "ethers";
  import detectEthereumProvider from "@metamask/detect-provider";
  import { AD3Token } from "@/../AD3Token.json";
  
  export default {
    data() {
      return {
        isConnected: false,
        connectedAddress: "",
        balance: null,
      };
    },
    methods: {
      async connectMetaMask() {
        const provider = await detectEthereumProvider();
  
        if (provider) {
          try {
            const accounts = await provider.request({
              method: "eth_requestAccounts",
            });
            this.connectedAddress = accounts[0];
            this.isConnected = true;
          } catch (error) {
            console.error("Error connecting to MetaMask:", error);
          }
        } else {
          console.log("MetaMask not detected. Please install it.");
        }
      },
      async fetchBalance() {
        const provider = new ethers.providers.JsonRpcProvider(
        // 'https://alpha-rpc.scroll.io/l2'
        'https://endpoints.omniatech.io/v1/eth/goerli/public'
        );
  
        const erc20ABI = [AD3Token];
  
        const tokenContractAddress = "0x6e6B4C38f3dA63763f87737688f7425dB380a4ac";
        const tokenContract = new ethers.Contract(
          tokenContractAddress,
          erc20ABI,
          provider
        );
  
        try {
          const balance = await tokenContract.balanceOf(this.connectedAddress);
          this.balance = balance.toString();
        } catch (error) {
          console.error("Error fetching balance:", error);
          this.balance = "Error fetching balance";
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .erc20-metamask {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: Arial, sans-serif;
  }
  </style>
  