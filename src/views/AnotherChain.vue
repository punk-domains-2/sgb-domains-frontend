<template>
<div class="container">
  <h1 class="text-center">Buy domain on another chain</h1>
  
  <div class="row mt-3 text-center">
    <div class="col-md-8 offset-md-2 mb-3">
      <p class="mt-4">
        Pay for domain on another blockchain using Flare's Data Connector.
      </p>

      <hr class="mt-4" />

      <h3 class="mt-4">Step 1: Payment instructions</h3>

      <p class="mt-3">
        Enter desired domain name and choose a blockchain in a dropdown.
      </p>

      <div class="row mt-2">
        <div class="col-md-6 offset-md-3">
          <input 
            v-model="domainName"
            class="form-control text-center border-2 border-light"
            placeholder="Enter a domain name"
          >
        </div>
      </div>

      <div class="btn-group mt-3">
        <button class="btn btn-primary dropdown-toggle" type="button" id="dropdownMenuButton2" data-bs-toggle="dropdown" aria-expanded="false">
          {{ blockchain }}
        </button>
        <ul class="dropdown-menu dropdown-menu-start" aria-labelledby="dropdownMenuButton2">
          <li v-for="chain in chainNames" :key="chain" class="dropdown-item" @click="blockchain=chain">{{ chain }}</li>
        </ul>
      </div>

      <p class="mt-3" v-if="blockchain in chains && domainName">
        Send {{ price }} {{ currency }} to this address on {{ blockchain }}: {{ recipientAddress }}
      </p>

      <hr class="mt-4" />

      <h3 class="mt-4">Step 2: Mint domain</h3>

      <p class="mt-3">Enter the payment transaction hash and the domain name you want to mint.</p>

      <div class="row mt-2">
        <div class="col-md-6 offset-md-3">
          <input 
            v-model="txHash"
            class="form-control text-center border-2 border-light"
            placeholder="Enter transaction hash"
          >
        </div>
      </div>

      <div class="btn-group mt-3">
        <button class="btn btn-primary dropdown-toggle" type="button" id="dropdownMenuButton2" data-bs-toggle="dropdown" aria-expanded="false">
          Paid on {{ blockchain }}
        </button>
        <ul class="dropdown-menu dropdown-menu-start" aria-labelledby="dropdownMenuButton2">
          <li v-for="chain in chainNames" :key="chain" class="dropdown-item" @click="blockchain=chain">Paid on {{ chain }}</li>
        </ul>
      </div>

      <div class="row mt-3">
        <div class="col-md-6 offset-md-3">
          <input 
            v-model="domainName"
            class="form-control text-center border-2 border-light"
            placeholder="Enter a domain name"
          >
        </div>
      </div>

      <button 
        class="btn btn-primary mt-3" 
        @click="mintDomain" 
        :disabled="waitingMint"
      >
        <span v-if="waitingMint" class="spinner-border spinner-border-sm mx-1" role="status" aria-hidden="true"></span>
        <span>Mint domain</span>
      </button>

      <!-- Error message -->
      <div class="alert alert-danger mt-3" v-if="errorMessage">
        {{ errorMessage }}
      </div>

      <!-- Success message -->
      <div class="alert alert-success mt-3" v-if="successMessage">
        {{ successMessage }}
      </div>

    </div>
  </div>
</div>
</template>

<script lang="ts">
import axios from "axios"
import { ethers } from "ethers"
import { useEthers } from 'vue-dapp';
import { useToast, TYPE } from "vue-toastification";

export default {
  name: "AnotherChain",

  data() {
    return {
      apiBaseUrl: null,
      blockchain: "Select blockchain",
      chains: {
        "Ethereum": {
          "blockExplorerUrl": "https://etherscan.io/",
          "chainApiCode": "eth",
          "chainName": "Ethereum",
          "recipientAddress": "0x6771F33Cfd8C6FC0A1766331f715f5d2E1d4E0e2",
          "tokenSymbol": "USDC",
          "prices": { // prices should be static, because if they change, proof validation may fail
            5: 2, // 5-char domain
            4: 10, // 4-char domain
            3: 100, // 3-char domain
            2: 500, // 2-char domain
            1: 2000, // 1-char domain
          }
        },
      },
      domainName: "",
      errorMessage: null,
      successMessage: null,
      txHash: "",
      waitingMint: false,
    }
  },

  mounted() {
    this.apiBaseUrl = import.meta.env.VITE_APP_API_BASE_URL
    this.fetchDomainPrices()
  },

  computed: {
    blockExplorerUrl() {
      if (this.chains && this.blockchain in this.chains) {
        return this.chains[this.blockchain].blockExplorerUrl
      }

      return null
    },

    chainNames() {
      return Object.keys(this.chains)
    },
    
    currency() {
      if (this.chains && this.blockchain in this.chains) {
        return this.chains[this.blockchain].tokenSymbol
      }

      return null
    },

    price() {
      if (this.chains && this.blockchain in this.chains && this.domainName) {
        if (this.domainName.length > 5) {
          return this.chains[this.blockchain].prices[5]
        }

        return this.chains[this.blockchain].prices[this.domainName.length]
      }

      return null
    },

    recipientAddress() {
      if (this.chains && this.blockchain in this.chains) {
        return this.chains[this.blockchain].recipientAddress
      }

      return null
    },
  },

  methods: {
    async fetchDomainPrices() {
      const response = await axios.get(`${this.apiBaseUrl}/domain-prices`)
      this.chains = response.data
    },

    async mintDomain() {
      if (!this.address) {
        this.errorMessage = "Please connect your wallet"
        this.waitingMint = false
        return
      }

      this.errorMessage = null
      this.successMessage = null
      this.waitingMint = true

      // check if transaction hash is entered
      if (!this.txHash) {
        this.errorMessage = "Please enter a transaction hash"
        this.waitingMint = false
        return
      }

      // transaction hash validation
      try {
        if (!ethers.utils.isHexString(this.txHash, 32)) {
          this.errorMessage = "Invalid transaction hash format"
          this.waitingMint = false
          return
        }
      } catch (error) {
        this.errorMessage = "Invalid transaction hash"
        this.waitingMint = false
        return
      }

      // check if blockchain is selected
      if (this.blockchain === "Select blockchain") {
        this.errorMessage = "Please select a blockchain"
        this.waitingMint = false
        return
      }

      // check if domain name is entered
      if (!this.domainName) {
        this.errorMessage = "Please enter a domain name"
        this.waitingMint = false
        return
      }

      const chainApiCode = this.chains[this.blockchain].chainApiCode

      if (!chainApiCode) {
        this.errorMessage = "Invalid blockchain"
        this.waitingMint = false
        return
      }
      
      // send data to the API
      try {
        const response = await axios.post(`${this.apiBaseUrl}/verify-payment`, {
          tx: this.txHash,
          domain: this.domainName,
          chain: chainApiCode,
          user: this.address,
        })

        if (response.status === 200) {
          this.toast("Minting request sent!", { type: TYPE.SUCCESS });
          this.successMessage = "Minting request sent! Your domain will be minted to your wallet in a few minutes. If it doesn't show up, please contact us via Discord (link in the footer)."
        }
      } catch (error) {
        console.error(error)
        this.errorMessage = error.response?.data?.message || "Minting request failed!"
        this.toast(this.errorMessage, { type: TYPE.ERROR });
      } finally {
        this.waitingMint = false
      }

      // TODO: delete txHash and domainName from the state
    },
  },

  setup() {
    const { address, signer } = useEthers();
    const toast = useToast();

    return { address, signer, toast }
  },
}
</script>
