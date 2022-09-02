<template>
  <div class="">
    <h2 v-if="!accountConnected" class="title">
      Connect your wallet
    </h2>
    <h2 v-else class="title">
      Submit to Effect Network
    </h2>

    <div v-if="accountConnected" class="box media">
      <figure class="media-left">
        <p class="image is-128x128">
          <img v-if="connectAccount.providerName === 'metamask'" src="@/assets/images/providers/BSC-logo.svg" alt="" srcset="">
          <img v-else src="@/assets/images/providers/EOS-logo.svg" alt="" srcset="">
        </p>
      </figure>

      <div class="media-content">
        <div class="content">
          <p class="subtitle">
            Connected
          </p>
          <p>
            <strong>{{ connectResponse.accountName }}</strong>
          </p>
          <hr>
          <p class="subtitle">
            Order
          </p>

          <table class="table is-narrow is-centered">
            <thead />
            <tbody>
              <tr>
                <td>Tasks</td>
                <td>{{ batch.length }}&nbsp;×</td>
              </tr>
              <tr>
                <td>Amount</td>
                <td>{{ repetitions }}&nbsp;×</td>
              </tr>
              <tr>
                <td>Cost per Task</td>
                <td><strong>{{ campaign.info.reward }}</strong> <i>{{ client.config.efxSymbol }}</i></td>
              </tr>
            </tbody>

            <tfoot>
              <tr>
                <td>Total Cost</td>
                <td><strong>{{ batchCost }}</strong> <i>{{ client.config.efxSymbol }}</i></td>
              </tr>
            </tfoot>
          </table>
        </div>
      </div>
    </div>

    <div v-if="!accountConnected" id="connect-buttons" class="buttons px-6">
      <button id="btn-login" class="button is-large is-fullwidth is-light  px-6 mx-6" @click="login()">
        <span class="icon">
          <img src="@/assets/images/providers/BSC-logo.svg" alt="" srcset="">
        </span>
        <span>Connect with BSC</span>
      </button>
      <button id="btn-login-eos" class="button is-large is-fullwidth is-light  px-6 mx-6" @click="loginEOS()">
        <span class="icon">
          <img src="@/assets/images/providers/EOS-logo.svg" alt="" srcset="">
        </span>
        <span>Connect with EOS</span>
      </button>
    </div>

    <div v-if="accountConnected && !createdBatchId">
      <p v-if="paymentLoading" class="notification is-warning">
        Please be patient when posting...it can take up to a minute for transaction to complete.
        <br>
        Also note that you will need to sign multiple transactions if you are using Metamask.
      </p>
      <form @submit.prevent="uploadBatch">
        <div class="field is-grouped is-justify-content-center mt-6">
          <div class="control">
            <button class="button is-outlined is-primary is-wide" @click="previousStep">
              Back
            </button>
            <button type="submit" :class="{'is-loading': loading}" class="button button is-primary is-wide mr-4">
              Post tasks
            </button>
          </div>
        </div>
      </form>
    </div>

    <div v-if="createdBatchId" class="notification is-success">
      <p class="mx-6 px-6 has-text-centered">
        <strong>Success!</strong><br>
        Your order has been successfuly posted to: <br>
        <a :href="`https://app.effect.network/campaigns/${campaign.id}/${createdBatchId}`" target="_blank" rel="noopener noreferrer">Effect Force <font-awesome-icon class="icon mx-2 is-small" icon="fa-solid fa-arrow-up-right-from-square" /></a>
        <br>
      </p><hr>
      <div class="buttons is-centered">
        <a :href="'/batch/' + createdBatchId" class="mx-6 px-6 button is-centered " target="" rel="noopener noreferrer">
          Go to results
        </a>
      </div>
    </div>
  </div>
</template>
<script>
// import { mapActions } from 'vuex'
import Vue from 'vue'
import Web3 from 'web3'
import * as effectsdk from '@effectai/effect-js'
import AnchorLink from 'anchor-link'
import AnchorLinkBrowserTransport from 'anchor-link-browser-transport'
export default Vue.extend({
  components: {},
  props: ['batch', 'campaign', 'repetitions'],
  data () {
    return {
      createdBatchId: null,
      loading: false,
      account: null,
      client: null,
      campaignid: null,
      batchidentification: null,
      connectAccount: {
        providerName: null,
        provider: null,
        account: null
      },
      connectResponse: null,
      message: null,
      accountConnected: false,
      paymentLoading: false
    }
  },
  computed: {
    ethereumConnected () {
      return true
    },
    batchCost () {
      return (this.batch.length * this.repetitions) * this.campaign.info.reward
    }
  },
  created () {

  },
  methods: {
    async uploadBatch () {
      this.paymentLoading = true
      try {
        this.loading = true

        const content = {
          tasks: this.batch.map(el => ({ place_holder: el.place_holder }))
        }
        console.log('uploading batch', content)
        // Show how user can set up their own proxy contract in order to post tasks.
        console.log(this.campaign.id, content, Number(this.repetitions), 'efxtaskproxy')
        const result = await this.client.force
          .createBatch(this.campaign.id, content, Number(this.repetitions), 'efxtaskproxy')
          .catch(error => console.error('Failed to create batch', error))
        console.log('result', result)
        this.createdBatchId = await this.client.force.getBatchId(result.id, this.campaign.id)
        // this.$emit('success', 'Tasks successfuly uploaded to Effect Force!')

        console.log('batch created', this.createdBatchId)
      } catch (e) {
        this.$emit('error', e)
        console.error(e)
      }
      this.loading = false
      this.paymentLoading = false
    },
    previousStep () {
      this.$emit('previousStep')
    },
    async login () {
      try {
        this.generateClient()
        await this.connectMetamask()
        await this.connectEffectAccount()
      } catch (error) {
        console.error(error)
        this.$emit('error', error)
      }
    },
    async loginEOS () {
      try {
        this.generateClient()
        await this.connectAnchor()
        await this.connectEffectAccount()
      } catch (error) {
        console.error(error)
        this.$emit('error', error)
      }
    },
    /**
    * SDK Client
    * Create a new Effect SDK client.
    */
    generateClient () {
      console.log('Creating SDK...')
      try {
        this.client = new effectsdk.EffectClient('mainnet')
        console.log(this.client)
      } catch (error) {
        console.error(error)
      }
    },
    /**
    * EOS Anchor Wallet
    */
    async connectAnchor () {
      try {
        const transport = new AnchorLinkBrowserTransport()
        const alink = new AnchorLink({
          transport,
          chains: [
            {
              chainId: this.client.config.eosChainId,
              nodeUrl: this.client.config.eosNodeUrl
            }
          ]
        })
        // Perform the login, which returns the users identity
        const identity = await alink.login('hackathon-boilerplate')
        const { session } = identity
        const signatureProvider = session.makeSignatureProvider()
        const account = { accountName: session.auth.actor.toString(), permission: session.auth.permission.toString() }
        console.log('Logged in as', account)
        this.connectAccount.provider = signatureProvider
        this.connectAccount.account = account
        this.connectAccount.providerName = 'anchor'
      } catch (error) {
        this.$emit('error', error.message)
        console.error(error)
      }
    },
    /**
    * Metamask
    * Generate web3 instance from account with private key.
    * Could also be the web3 object with a metamask connection.
    *
    * Here we will also make a call to make sure we are on the correct chain.
    * Bsc-Mainnet: 0x38 (hex), 56 (decimal)
    * Bsc-Testnet: 0x61 (hex), 97 (decimal)
    */
    async connectMetamask () {
      console.log('Connecting to metamask wallet.')
      // @ts-ignore
      if (window.ethereum) {
        try {
          console.log('Connecting to metamask wallet.')
          // @ts-ignore
          // eslint-disable-next-line no-unused-vars
          const ethAccount = await window.ethereum.request({ method: 'eth_requestAccounts' })
          // @ts-ignore
          await window.ethereum.request({
            method: 'wallet_switchEthereumChain',
            params: [{ chainId: '0x38' }] // 0x38 is the chainId of bsc testnet.
          })
          // @ts-ignore
          this.connectAccount.provider = new Web3(window.ethereum)
          this.connectAccount.account = null
          this.connectAccount.providerName = 'metamask'
        } catch (error) {
          this.$emit('error', error)
          console.error('Is this it????', error)
        }
      } else {
        this.$emit('error', 'Metamask not installed')
      }
    },
    /**
    * Connect to Effect Account using burnerwallet, metamask or anchor
    */
    async connectEffectAccount () {
      console.log('Connecting to account with wallet.')
      try {
        if (this.connectAccount.provider) {
          this.connectResponse = await this.client.connectAccount(this.connectAccount.provider, this.connectAccount.account)
        } else {
          this.$emit('error', 'Login failed, try again.')
          return
        }
        this.accountConnected = true
        this.account = this.connectResponse
        this.$emit('account', this.connectResponse, this.client)
      } catch (error) {
        this.accountConnected = false
        this.$emit('error', 'Login failed, try again.')
        console.error(error)
      }
    }
  }
})
</script>
