<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h1 v-text='tit'></h1>
    <input v-model='newItem' v-on:keyup.enter="addNew">
    <ul>
      <li v-for='item in items' v-bind:class='{finished: item.finished}' @click='toggleFinished(item)'>{{item.coding}}</li>
    </ul>
  </div>
</template>

<script type="text/ecmascript-6">
import Store from '../store.js';
export default {
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to Myc',
      tit: '你好，vue',

      items: Store.fetch(),
      newItem: ''
    };
  },
  watch: {
    items: {
      handler: function (items) {
        // console.log(val, oldVla)
        Store.save(items);
      },
      deep: true
    }
  },
  methods: {
    toggleFinished (item) {
      item.finished = !item.finished;
    },

    addNew () {
      this.items.push({
        coding: this.newItem,
        finished: false
      });
      // console.log(this.items)
      this.newItem = '';
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.finished{
  text-decoration: underline;
}
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  /*display: inline-block;*/
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
