<template>
  <header>
    <nav :class="['nav', {'nav-active': scrollTop > 0}]">
      <a class="logo"><img src="/logo.png" alt="Logo"></a>
      <router-link to="/">所有众筹</router-link>
      <router-link to="/myself">我的众筹</router-link>
      <span :style="{ flex: 1 }"></span>
      <a @click="handleClick">{{ account }}</a>
    </nav>
    <h1 class="title">
      Dapp众筹平台
    </h1>
  </header>
</template>

<script lang="ts">
import { ref, onMounted, defineComponent } from 'vue'
import { authenticate, getAccount, addListener } from '../api/contract'

export default defineComponent({
  setup() {
    const scrollTop = ref(0)
    onMounted(() => {
      window.addEventListener('scroll', () => {
        scrollTop.value = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
      })
    })

    const account = ref('认证')
    async function handleClick() {
      await authenticate()
      account.value = await getAccount()
    }

    handleClick()
    addListener(handleClick)

    return { scrollTop, handleClick, account }
  }
})
</script>

<style scoped>
header {
  height: 100vh; /* Set header height to fill the entire viewport */
  background: url("/header.png") no-repeat center center/contain; /* Ensure the image scales proportionally */
  background-color: #f0f0f0; /* Fallback background color */
  position: relative;
}

.nav {
  display: flex;
  align-items: center;
  padding: 0 2em; /* Adjusted for better responsiveness */
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 10;
  transition: all 0.3s ease;
  background-color: rgba(255, 255, 255, 0.8); /* Slight transparency for better contrast */
}

@media screen and (max-width: 800px) {
  .nav {
    padding: 0 1em; /* Adjust padding for smaller screens */
  }
}

.nav a {
  line-height: 50px;
  padding: 0 1em;
  border: 3px solid transparent;
  color: black; /* Set font color to black */
  text-decoration: none; /* Remove underline */
  transition: all 0.3s ease;
}

.nav a:hover {
  background: var(--hover-background);
  border-top-color: var(--hover-color);
  color: var(--hover-color); /* Optional: Change text color on hover */
}

.nav a.router-link-active, .nav a.router-link-exact-active {
  border-top-color: var(--choose-color);
  color: var(--choose-color); /* Active link color */
}

.nav .logo {
  padding: 0;
}

.nav .logo img {
  height: 50px; /* Logo size */
  opacity: 1; /* Ensure logo is visible */
  transition: opacity 0.3s ease;
}

.nav:hover, .nav.nav-active {
  background: #fff;
  box-shadow: var(--shadow);
}

.nav:hover a, .nav.nav-active a {
  color: #333; /* Set font color to dark on hover */
}

.title {
  position: absolute;
  left: 4em;
  top: 3em;
  color: white; /* Title color */
  text-shadow: 2px 2px 10px rgba(255, 0, 0, 0.5); /* Subtle red shadow */
  font-size: 3em; /* Increase the font size */
  font-weight: bold;
}

</style>