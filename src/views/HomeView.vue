<template>
  <div class="home">
    
    <Header>

    </Header>



  </div>
</template>

<script>
// @ is an alias to /src
import Header from '@/components/Header.vue'


export default {
  name: 'HomeView',
  components: {
    Header,
  }
}
</script>

<style lang="scss" scoped>
.home{
  padding: 0 0 50px 0;
}
</style>
