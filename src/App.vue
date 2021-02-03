<template>
  <div id="app">
    <Quiz />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Quiz from './components/Quiz.vue';

@Component({
  components: {
    Quiz,
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">
* {
  padding: 0;
  margin: 0;
}

button {
  border: none;
  margin: 0;
  padding: 0;
  width: auto;
  overflow: visible;

  background: transparent;

  /* inherit font & color from ancestor */
  color: inherit;
  font: inherit;

  /* Normalize `line-height`. Cannot be changed from `normal` in Firefox 4+. */
  line-height: normal;

  /* Corrects font smoothing for webkit */
  -webkit-font-smoothing: inherit;
  -moz-osx-font-smoothing: inherit;

  /* Corrects inability to style clickable `input` types in iOS */
  -webkit-appearance: none;
  
  &::-moz-focus-inner {
    border: 0;
    padding: 0;
  }
  

/* ------------------------- */

  border: 2px solid #87ECFA;
  border-radius: 0.5rem;
  color: #87ECFA;
  margin: 0;
  padding: 0.3rem 0.8rem;
  cursor: pointer;

  &:hover {
    background-color: #b6f6ff;
    color: white;
  }

  &:disabled, button[disabled] {
    background-color: #fefefe;
    border-color: #e0e0e0;
    color: #e0e0e0;
    &:hover {
      color: #e0e0e0;
    }
  }
  &:active {
    outline: none;
  }
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
