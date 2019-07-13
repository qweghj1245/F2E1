<template>
  <div id="app">
    <router-view style="margin-top:auto"/>
    <div class="footer">POMODORO</div>
  </div>
</template>

<script>

export default {};
</script>

<style lang="scss">
@import './assets/icomoon/style.css';
  html, body {
    margin: 0;
    padding: 0;
    height: 100%;
  }
  * {
    box-sizing: border-box;
  }
  #app {
    min-width: 1280px;
    margin: 0 auto;
    background-color: #ffe9dc;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    height: 100%;
    position: relative;
    .footer {
      background-color: #F25044;
      font-size: 24px;
      font-weight: bold;
      color: #FFE9DC;
      width: 100%;
      text-align: center;
      height: 35px;
      line-height: 35px;
      margin-top: auto;
    }
  }
</style>
