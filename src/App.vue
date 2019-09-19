<template>
  <div id="app">
    <virtualList :data="list">
      <template slot-scope="scope">
        <div class="list_item">{{ scope.row.name }}</div>
      </template>
    </virtualList>
  </div>
</template>

<script lang="ts">
import { Component, Provide, Vue } from 'vue-property-decorator'
import virtualList from './components/virtual-list.vue'

@Component({
  components: {
    virtualList
  }
})
export default class App extends Vue {
  list: any = []

  created () {
    const tmp = []
    for (let index = 0; index < 10000; index++) {
      tmp.push({
        id: index,
        name: 'name' + index
      })
    }
    this.list = tmp
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.list_item{
  height: 150px;
  width: 300px;
  border: 1px solid gray;
  margin: 10px 0px;
  border-radius: 10px;
}
</style>
