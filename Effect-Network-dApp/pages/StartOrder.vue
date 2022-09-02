<template>
  <div class="section pt-6">
    <div class="columns">
      <div class="column is-three-fifths is-offset-one-fifth">
        <div v-if="step === 1" id="step-1">
          <h2 class="title">
            1. Start
          </h2>
          <div class="control">
            <div class="buttons is-centered px-6 mx-6">
              <button class="button is-link is-light mt-3 is-fullwidth is-large" @click.prevent="type='test'; nextStep()">
                <span><font-awesome-icon class="mx-2 icon is-small" icon="fa-solid fa-heart" /></span>
                <span>&nbsp;Create New Task</span>
              </button>
            </div>
          </div>
        </div>

        <task-form
          v-if="step === 2"
          :campaign="campaigns[type]"
          @error="setErrorMessage"
          @setBatch="setBatch"
          @previousStep="previousStep()"
          @nextStep="nextStep()"
        />
        <wallet-login
          v-if="step === 3"
          :repetitions="repetitions"
          :batch="batch"
          :campaign="campaigns[type]"
          @previousStep="previousStep()"
          @error="setErrorMessage"
          @success="setSuccessMessage"
          @account="setAccount"
        />
        <article v-if="successMessage" class="message is-success mt-5">
          <div class="message-body">
            {{ successMessage }}
          </div>
        </article>

        <article v-if="errorMessage" class="message is-danger mt-5">
          <div class="message-body">
            {{ errorMessage }}
          </div>
        </article>
      </div>
    </div>
  </div>
</template>

<script>
// Import Effect-JS
import * as effectsdk from '@effectai/effect-js'

export default {
  data () {
    return {
      loading: false,
      batch: null,
      repetitions: 1,
      step: 1,
      account: null,
      effectsdk: null,
      efxAvailable: null,
      campaigns: {
        test: null
      },
      type: null,
      successMessage: null,
      errorMessage: null
    }
  },
  computed: {
  },
  created () {
    // When the component is created we retrieve the campaigns
    console.log(this.$config)
    this.getCampaigns()
  },
  methods: {
    setSuccessMessage (msg) {
      this.successMessage = msg
    },
    setErrorMessage (msg) {
      this.errorMessage = msg
      setTimeout(() => { this.errorMessage = null }, 5000)
    },
    nextStep () {
      this.successMessage = null
      this.step += 1
    },
    previousStep () {
      this.errorMessage = null
      this.successMessage = null
      this.step -= 1
    },
    /**
     * Get all campaign data from Effect Force
     * First initialize the SDK and then retrieve the campaigns
     */
    async getCampaigns () {
      try {
        this.effectsdk = new effectsdk.EffectClient('mainnet')
        this.campaigns.test = await this.effectsdk.force.getCampaign(27)
      } catch (error) {
        this.setErrorMessage(error)
        console.error(error)
      }
    },
    /**
     * Set the account to use for ordering
     */
    setAccount (account, sdk) {
      this.effectsdk = sdk
      this.account = account
    },
    /**
     * Set the batch to use for ordering
     */
    setBatch (batch, repetitions) {
      this.batch = batch
      this.repetitions = repetitions
    }
  }
}
</script>

<style>
</style>
