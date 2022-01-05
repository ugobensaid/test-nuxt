<template>
  <div>
    <p>{{count}}</p>
    <button @click="changeValue(-20)">+</button>
  </div>
</template>

<script>
    export default {
      name: "Counter",
      data() {
        return {
          count: 0
        }
      },
      methods: {
        changeValue(num) {
          this.count += num
        },
      },
    }
</script>

<style scoped>

</style>
