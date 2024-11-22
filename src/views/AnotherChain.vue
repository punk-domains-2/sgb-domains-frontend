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
            <li class="dropdown-item" @click="blockchain='Ethereum'">Ethereum</li>
            <li class="dropdown-item" @click="blockchain='Litecoin'">Litecoin</li>
          </ul>
        </div>

        <p class="mt-3" v-if="blockchain in prices && domainName">
          Send {{ price }} {{ currency }} to this address: {{ address }}
        </p>

        <hr class="mt-4" />

        <h3 class="mt-4">Step 2: Validate payment</h3>

        <p class="mt-3">Paste transaction hash here:</p>

        <div class="row mt-2">
          <div class="col-md-6 offset-md-3">
            <input 
              v-model="txHash"
              class="form-control text-center border-2 border-light"
              placeholder="Enter transaction hash"
            >
          </div>
        </div>

        <button 
          class="btn btn-primary mt-3" 
          @click="validatePayment" 
          :disabled="waitingValidation"
        >
          <span v-if="waitingValidation" class="spinner-border spinner-border-sm mx-1" role="status" aria-hidden="true"></span>
          <span>Validate payment</span>
        </button>

        <hr class="mt-4" />

        <h3 class="mt-4">Step 3: Mint domain</h3>

        <p class="mt-3">After your payment is validated, you can mint a domain.</p>

        <div class="row mt-2">
          <div class="col-md-6 offset-md-3">
            <input 
              v-model="txHash"
              class="form-control text-center border-2 border-light"
              placeholder="Enter transaction hash"
            >
          </div>
        </div>

        <div class="row mt-2">
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
          addresses: {
            "Ethereum": "0xE08033d0bDBcEbE7e619c3aE165E7957Ab577961",
            "Litecoin": "TBD",
          },
          blockchain: "Select blockchain",
          currencies: {
            "Ethereum": "ETH",
            "Litecoin": "LTC",
          },
          domainName: "",
          prices: {
            "Ethereum": {
              5: 0.001, // 5-char domain
              4: 0.003, // 4-char domain
              3: 0.022, // 3-char domain
              2: 0.129, // 2-char domain
              1: 0.49, // 1-char domain
            },
            "Litecoin": {
              5: 0.02, // 5-char domain
              4: 0.09, // 4-char domain
              3: 0.9, // 3-char domain
              2: 5, // 2-char domain
              1: 15, // 1-char domain
            }
          },
          txHash: "",
          waitingMint: false,
          waitingValidation: false,
        }
      },

      computed: {
        address() {
          if (this.blockchain in this.addresses) {
            return this.addresses[this.blockchain]
          }

          return null
        },
        currency() {
          if (this.blockchain in this.currencies) {
            return this.currencies[this.blockchain]
          }

          return null
        },

        price() {
          if (this.blockchain in this.prices && this.domainName) {

            if (this.domainName.length > 5) {
              return this.prices[this.blockchain][5]
            }

            return this.prices[this.blockchain][this.domainName.length]
          }

          return null
        }
      },

      methods: {
        mintDomain() {
          this.waitingMint = true

          // TODO: mint domain

          this.waitingMint = false
        },

        validatePayment() {
          this.waitingValidation = true

          // TODO: validate payment

          this.waitingValidation = false
        }
      }
    }
  </script>
