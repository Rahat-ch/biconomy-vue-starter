<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import { ref } from 'vue'
import { ChainId } from "@biconomy/core-types";
import { ethers } from 'ethers'
import { IBundler, Bundler } from '@biconomy/bundler'
import { BiconomySmartAccount, BiconomySmartAccountConfig, DEFAULT_ENTRYPOINT_ADDRESS } from "@biconomy/account"
import { 
  IPaymaster, 
  BiconomyPaymaster,  
} from '@biconomy/paymaster'

const bundler: IBundler = new Bundler({
  // get this from https://dashboard.biconomy.io/ - do not commit in production use env variables
  bundlerUrl: 'https://bundler.biconomy.io/api/v2/80001/nJPK7B3ru.dd7f7861-190d-41bd-af80-6877f74b8f44',
  chainId: ChainId.POLYGON_MUMBAI,
  entryPointAddress: DEFAULT_ENTRYPOINT_ADDRESS,
})

const paymaster: IPaymaster = new BiconomyPaymaster({
  // get this from https://dashboard.biconomy.io/ - do not commit in production use env variables
  paymasterUrl: "https://paymaster.biconomy.io/api/v1/80001/cU8AbO4sz.1e2049d7-ac7e-4c8b-a1d9-b8d3b663df7d"
})

const scAddress = ref("")
const smartAccount = ref({})

const handleWalletConnect = async () => {
    const { ethereum } = window;
    if (ethereum) {
      const provider = new ethers.providers.Web3Provider(ethereum)
      await provider.send("eth_requestAccounts", []);
      const signer = provider.getSigner();
      scAddress.value = "Loading smart account..."
      console.log({scAddress})
      try {
      const biconomySmartAccountConfig: BiconomySmartAccountConfig = {
        signer: signer,
        chainId: ChainId.POLYGON_MUMBAI,
        bundler: bundler,
        paymaster: paymaster
      }
      let biconomySmartAccount = new BiconomySmartAccount(biconomySmartAccountConfig)
      biconomySmartAccount =  await biconomySmartAccount.init()
      scAddress.value = await biconomySmartAccount.getSmartAccountAddress()
      smartAccount.value = biconomySmartAccount
    } catch (err) {
      console.log('error setting up smart account... ', err)
    }
    } else {
      alert('No Wallet Detected')
    }
  }
</script>

<template>
  <h1>Vue JS + Biconomy SDK starter</h1>
   <!-- <p v-if="scAddress">Smart Account: {{ scAddress }}</p> -->
   <h2 v-if="!scAddress">Connect to display your smart account</h2>
   <button v-if="!scAddress" type="button" @click="handleWalletConnect">Connect Wallet</button>
   <HelloWorld msg="Smart Account below" :address="scAddress" />
 </template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>



