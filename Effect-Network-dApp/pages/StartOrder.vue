<template>
  <div class="section pt-6">
    <div class="columns">
      <div class="column is-three-fifths is-offset-one-fifth">
        <task-form
          v-if="step === 1"
          :campaign="campaign"
          @error="setErrorMessage"
          @setBatch="setBatch"
          @previousStep="previousStep()"
          @nextStep="nextStep()"
        />
        <wallet-login
          v-if="step === 2"
          :repetitions="repetitions"
          :batch="batch"
          :campaign="campaign"
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
      campaign: null,
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
    this.getCampaign()
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
    async getCampaign () {
      try {
        this.effectsdk = new effectsdk.EffectClient('mainnet')
        this.campaign = await this.effectsdk.force.getCampaign(27)
        this.campaign.placeholders = this.getPlaceholders(this.campaign.info.template)
        console.log('this.campaign', this.campaign)
      } catch (error) {
        this.setErrorMessage(error)
        console.error(error)
      }
    },

    getPlaceholders (template) {
      const placeholders = getMatches(
        template,
        /\$\{\s?(\w+)\s?\|?\s?(\w*)\s?\}/g
      )
      return [...new Set(placeholders)]
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
function getMatches (string, regex, index) {
  index || (index = 1) // default to the first capturing group
  const matches = []
  let match
  while ((match = regex.exec(string))) {
    matches.push(match[index])
  }
  return matches
}
</script>

<style>
</style>
