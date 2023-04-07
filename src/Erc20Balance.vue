<template>
  <div class="erc20-balance">
    <h1>ERC20 Token Balance</h1>
    <div>
      <label for="address">Address:</label>
      <input type="text" v-model="address" placeholder="Enter an Ethereum address" />
    </div>
    <button @click="fetchBalance">Get Balance</button>
    <div v-if="balance !== null">
      <p>Balance: {{ balance }}</p>
    </div>
  </div>
</template>

<script>
import { ethers } from "ethers";

export default {
  data() {
    return {
      address: "",
      balance: null,
    };
  },
  methods: {
    async fetchBalance() {
      const provider = new ethers.providers.JsonRpcProvider(
        'https://alpha-rpc.scroll.io/l2'
      );

      const erc20ABI = [
        "function balanceOf(address account) external view returns (uint256)",
      ];

      const tokenContractAddress = "0x6e6B4C38f3dA63763f87737688f7425dB380a4ac";
      const tokenContract = new ethers.Contract(
        tokenContractAddress,
        erc20ABI,
        provider
      );

      try {
        const balance = await tokenContract.balanceOf(this.address);
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
.erc20-balance {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: Arial, sans-serif;
}

input {
  margin-bottom: 1rem;
}
</style>
