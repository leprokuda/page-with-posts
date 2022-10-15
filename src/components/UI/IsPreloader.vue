<template>
<span v-if="show" class="loader"></span>
</template>

<script>
  export default {
    name: 'is-preloader',
    data() {
      return {
        show: true
      }
    },
    mounted() {
      this.showToggle()
    },
    methods: {
      showToggle() {
        setTimeout(() => {
          this.show = false
        }, 1000)
      }
    },
  }
</script>

<style scoped>
  .loader {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    display: block;
    margin:15px auto;
    position: relative;
    background: #42c759;
    box-sizing: border-box;
    animation: shadowExpandX 0.5s linear infinite alternate;
  }

  @keyframes shadowExpandX {
    0% {
      box-shadow: 0 0, 0 0;
      color: rgba(51, 119, 183, 0.2);
    }
    100% {
      box-shadow: -24px 0, 24px 0;
      color: rgba(51, 64, 183, 0.8);
    }
  }
</style>