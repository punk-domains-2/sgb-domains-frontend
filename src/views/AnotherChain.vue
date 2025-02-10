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

        <p class="mt-3" v-if="blockchain in this.chains && domainName">
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
  
      </div>
    </div>
  </div>
  </template>
  
  <script lang="ts">
    export default {
      name: "AnotherChain",

      data() {
        return {
          blockchain: "Select blockchain",
          chains: {
            "Polygon": {
              "chainName": "Polygon",
              "recipientAddress": "0x6771F33Cfd8C6FC0A1766331f715f5d2E1d4E0e2",
              "tokenAddress": "0xc2132D05D31c914a87C6611C10748AEb04B58e8F",
              "tokenSymbol": "USDT",
              "prices": {
                5: 1, // 5-char domain
                4: 7, // 4-char domain
                3: 69, // 3-char domain
                2: 420, // 2-char domain
                1: 1337, // 1-char domain
              }
            },
            "Flare": {
              "chainName": "Flare",
              "recipientAddress": "0x6771F33Cfd8C6FC0A1766331f715f5d2E1d4E0e2",
              "tokenAddress": "0x4a771cc1a39fdd8aa08b8ea51f7fd412e73b3d2b",
              "tokenSymbol": "USDX",
              "prices": {
                5: 1, // 5-char domain
                4: 7, // 4-char domain
                3: 69, // 3-char domain
                2: 420, // 2-char domain
                1: 1337, // 1-char domain
              }
            },
          },
          domainName: "",
          txHash: "",
          waitingMint: false,
        }
      },

      computed: {
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

        tokenAddress() {
          if (this.chains && this.blockchain in this.chains) {
            return this.chains[this.blockchain].tokenAddress
          }

          return null
        },
      },

      methods: {
        mintDomain() {
          this.waitingMint = true

          // TODO: mint domain

          this.waitingMint = false
        },
      }
    }
  </script>
