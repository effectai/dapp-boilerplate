<template>
  <div>
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="container">
        <div class="navbar-brand">
          <nuxt-link class="navbar-item" to="/">
            Home
          </nuxt-link>
        </div>

        <div id="navbar" class="navbar-menu">
          <div class="navbar-start" />
          <div class="navbar-end is-align-items-center">
            <div class="navbar-item" exact-active-class="is-active">
              <nuxt-link to="/startorder" class="button is-secondary is-outlined" exact-active-class="is-active">
                Login
              </nuxt-link>
            </div>
          </div>
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loading: false
    }
  },
  computed: {
  },
  methods: {
    scrollToElement (ref) {
      let el = this.$refs[ref]
      console.log('scroll', el)
      if (!el) {
        el = document.getElementById(ref)
      }
      if (el) {
        // Use el.scrollIntoView() to instantly scroll to the element
        el.scrollIntoView({ behavior: 'smooth' })
      } else {
        this.$router.push(`/#${ref}`)
      }
    }
  }
}
</script>

<style lang="css">

</style>
