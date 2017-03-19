<template>
  <div @click='HelloAxios'>
     I am ratings !
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    methods: {
      HelloAxios () {
        this.axios({
          method: 'get',
          url: '/api/goods'
        }).then(m => console.log(m.data));
      }
    }
  };
  
</script>

<style lang='stylus' type="stylesheet/stylus">
  
</style>
