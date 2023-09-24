<template>
  <div class="container dark">
    <TodoApp />
  </div>
</template>

<script>
import TodoApp from './components/TodoApp.vue'

export default {
  name: 'App',
  components: {
    TodoApp
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lexend:wght@400;600;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Lexend', sans-serif;
  scroll-behavior: smooth;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

:root {
  --color-main: #f68657;
  --color-text: #383a3f;
  --color-dark: #1f2124;
  --color-gray: #677;
  --color-bg: #f3f6f9;
  --color-light: #acb4bd;
  --color-lighter: #f9f9f9;
  --color-white: #fff;
  --color-border:#e0e3e6;
}

.dark {
  --color-main: #f68657;
  --color-text: #d6d1d1;
  --color-dark: #f5f6f7;
  --color-gray: #999;
  --color-bg: #1f2124;
  --color-lighter: #292a2c;
  --color-white: #2e3235;
  --color-border:#252629;
}

.container {
  width: 100vw;
  height: 100vh;
  color: var(--color-text);
  background-color: var(--color-bg);
  display: flex;
  align-items: center;
}

#app {
  width: 100vw;
  min-height: 100vh;
  background-color: #1f2124;
}
</style>
