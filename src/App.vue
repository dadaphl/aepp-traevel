<template>
  <div id="app">
    <nav>
      <div class="inner">
        <router-link class='logo' to='/'>
          <img src="@/assets/header-logo.svg" alt=""/>
          <span>travel</span>
        </router-link>
      </div>
    </nav>
    <main>
      <router-view/>
    </main>
    <ae-footer />
  </div>
</template>

<script>
import Footer from '@/partials/footer/footer.vue'
export default {
  name: 'app',
  components: {
    AeFooter: Footer
  },
  computed: {
  }
}
</script>

<style src='./App.scss' lang='scss' />
