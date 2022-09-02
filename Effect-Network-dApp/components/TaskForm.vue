<template>
  <div>
    <div id="step-2">
      <h2 class="title">
        Add tasks
      </h2>
      <div class="field">
        <div class="box">
          <div style="background: #fff; border-radius: 8px" class="p-2">
            <table v-if="campaign && campaign.placeholders" class="table mx-auto">
              <thead>
                <tr>
                  <th v-for="placeholder in campaign.placeholders" :key="placeholder" class="task-placeholder-value has-text-left">
                    {{ placeholder }}
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(task, index) in tasks" :key="task.id">
                  <td v-for="placeholder in campaign.placeholders" :key="placeholder" class="task-placeholder-value has-text-left">
                    <span>{{ tasks[index] }}</span>
                  </td>
                  <td>
                    <button class="button is-danger is-outlined is-small is-rounded" @click.prevent="tasks.splice(index, 1)">
                      X
                    </button>
                  </td>
                </tr>
                <tr>
                  <td v-for="(placeholder, placeindex) in campaign.placeholders" :key="placeholder" class="task-placeholder-value">
                    <input
                      :ref="`placeholder-${placeindex}`"
                      v-model="newTask[placeholder]"
                      type="url"
                      pattern="https?://.+"
                      class="input is-info task-placeholder-value"
                      :placeholder="placeholder"
                      required
                      @keydown.enter.prevent="createTask"
                    >
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <div class="control has-text-centered mt-5">
            <button class="button is-primary is-wide" @click.prevent="createTask">
              Add Task
            </button>
          </div>
        </div>
        <div class="box is-centered">
          <div v-if="campaign && campaign.info" class=" is-6 py-0 px-2 batch-info">
            <div class="box">
              <span>
                Amount:
              </span>
              <span>
                <strong>{{ repetitions }}</strong>
              </span>
              <input
                v-model="repetitions"
                class="slider is-fullwidth is-info"
                step="1"
                min="1"
                max="20"
                type="range"
              >
              Total Cost
              <strong>{{ parseFloat(campaign.info.reward * tasks.length * repetitions).toFixed(4) }} EFX</strong>
            </div>
          </div>
          <!-- </div> -->
        </div>
      </div>

      <form>
        <div class="field is-grouped is-justify-content-center mt-6">
          <div class="control">
            <button class="button is-outlined is-primary is-wide" @click="cancel">
              Back
            </button>
            <button type="submit" :class="{'is-loading': loading}" class="button button is-primary is-wide mr-4" :disabled="!tasks.length" @click="nextStep">
              Next step
            </button>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>
<script>
import Vue from 'vue'

export default Vue.extend({
  props: ['campaign'],
  data () {
    return {
      step: 1,
      repetitions: 1,
      tempCounter: 0,
      newTask: {
        id: null,
        link: null
      },
      tasks: [],
      error: null,
      loading: false,
      page: 1,
      type: null
    }
  },
  watch: {
    campaign () {
      this.newTask = this.getEmptyTask(this.campaign.placeholders)
    }
  },
  mounted () {
    if (this.campaign && this.campaign.placeholders) {
      this.newTask = this.getEmptyTask(this.campaign.placeholders)
    }
  },
  methods: {
    /**
     * Push new task in to the tasks array and create a prepare a new task.
     */
    createTask () {
      this.tasks.push(this.newTask)
      // Reset the newTask object
      this.newTask.id = this.tempCounter++
      this.newTask = this.getEmptyTask()
      this.$nextTick(() => {
        this.$refs['placeholder-0'][0].focus()
      })
    },
    getEmptyTask () {
      const emptyTask = {}
      this.campaign.placeholders.forEach((placeholder) => {
        emptyTask[placeholder] = ''
      })
      return emptyTask
    },
    nextStep () {
      this.$emit('nextStep')
      this.$emit('setBatch', this.tasks, this.repetitions)
    },
    cancel () {
      this.$emit('previousStep')
    }
  }
})
</script>

<style>
</style>
