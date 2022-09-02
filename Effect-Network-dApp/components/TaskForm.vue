<template>
  <div>
    <div id="step-2">
      <h2 class="title">
        2. Add placeholder
      </h2>
      <div class="field">
        <div class="box">
          <div style="background: #fff; border-radius: 8px" class="p-2">
            <table class="table mx-auto">
              <thead>
                <tr>
                  <th v-for="placeholder in placeholders" :key="placeholder" class="task-placeholder-value has-text-left">
                    {{ placeholder }}
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(task, index) in paginatedTasks" :key="task.id">
                  <td v-for="placeholder in placeholders" :key="placeholder" class="task-placeholder-value has-text-left">
                    <span>{{ tasks[index] }}</span>
                  </td>
                  <td>
                    <button class="button is-danger is-outlined is-small is-rounded" @click.prevent="tasks.splice(index, 1)">
                      <font-awesome-icon class="icon is-small" icon="fa-solid fa-trash-can" />
                    </button>
                  </td>
                </tr>
                <tr>
                  <td v-for="(placeholder, placeindex) in placeholders" :key="placeholder" class="task-placeholder-value">
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
            <pagination
              v-if="tasks"
              :items="tasks.length"
              :page="page"
              :per-page="perPage"
              @setPage="setPage"
            />
          </div>
          <div class="control has-text-centered mt-5">
            <button class="button is-primary is-wide" @click.prevent="createTask">
              Add Task
            </button>
            <div v-if="placeholderError" class="notification is-danger is-light mt-5">
              {{ placeholderError }}
            </div>
          </div>
        </div>
        <div v-if="tasks.length === 0" class="box is-centered">
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
import Pagination from './Pagination.vue'

function getMatches (string, regex, index) {
  index || (index = 1) // default to the first capturing group
  const matches = []
  let match
  while ((match = regex.exec(string))) {
    matches.push(match[index])
  }
  return matches
}

export default Vue.extend({
  components: { Pagination },
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
      perPage: 10,
      type: null,
      placeholders: ['link'],
      placeholderError: null,
      testCampaign: { id: 27, title: 'test' }
    }
  },
  computed: {
    paginatedTasks () {
      const start = (this.page - 1) * this.perPage
      if (this.tasks) {
        return this.tasks.slice(start, start + this.perPage)
      }
      return []
    }
  },
  mounted () {
    this.getPlaceholders(this.campaign.info.template)
    this.newTask = this.getEmptyTask(this.placeholders)
  },
  created () {
  },
  methods: {
    setPage (newPage) {
      this.page = newPage
    },
    /**
     * Push new task in to the tasks array and create a prepare a new task.
     */
    createTask () {
      // Check that all of the placeholders have been filled in.
      for (const key in this.newTask) {
        if (Object.hasOwnProperty.call(this.newTask, key)) {
          const element = this.newTask[key]
          if (element === null || element === '') {
            this.placeholderError = 'Please fill in all the placeholders'
            setTimeout(() => { this.placeholderError = null }, 5e3)
            return
          } else {
            // This NEEDS to be added as an object to the tasks array.
            this.tasks.push(this.newTask)
          }
        } else {
          this.placeholderError = 'Please fill in all the placeholders'
          setTimeout(() => { this.placeholderError = null }, 5e3)
          return
        }
      }

      // Reset the newTask object
      this.newTask.id = this.tempCounter++
      this.newTask = this.getEmptyTask(this.placeholders)
      this.$nextTick(() => {
        this.$refs['placeholder-0'][0].focus()
      })
    },
    getPlaceholders (template) {
      const placeholders = getMatches(
        template,
        /\$\{\s?(\w+)\s?\|?\s?(\w*)\s?\}/g
      )
      const unique = [...new Set(placeholders)]
      this.placeholders = unique
    },
    getEmptyTask (placeholders) {
      const emptyTask = {}
      placeholders.forEach((placeholder) => {
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
