<template>
  <div class="hello">
    <el-button @click="visible = true">Button</el-button>
    <el-dialog v-model="visible" title="Hello world">
      <p>Try Element</p>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'hello',
    data () {
      return {
        visible: false
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
