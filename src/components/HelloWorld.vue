<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <ul>
      <li>
        <a href="https://vuejs.org" target="_blank">Core Docs</a>
      </li>
      <li>
        <a href="https://forum.vuejs.org" target="_blank">Forum</a>
      </li>
      <li>
        <a href="https://chat.vuejs.org" target="_blank">Community Chat</a>
      </li>
      <li>
        <a href="https://twitter.com/vuejs" target="_blank">Twitter</a>
      </li>
      <br />
      <li>
        <a href="http://vuejs-templates.github.io/webpack/" target="_blank">Docs for This Template</a>
      </li>
    </ul>
    <div @click="show=true">
      <input type="text" style="color:#000" :value="resultReverse" disabled />
      <input type="hidden" name="departure" :value="result" />
    </div>
    <h2>Ecosystem</h2>
    <ul>
      <li>
        <a href="http://router.vuejs.org/" target="_blank">vue-router</a>
      </li>
      <li>
        <a href="http://vuex.vuejs.org/" target="_blank">vuex</a>
      </li>
      <li>
        <a href="http://vue-loader.vuejs.org/" target="_blank">vue-loader</a>
      </li>
      <li>
        <a href="https://github.com/vuejs/awesome-vue" target="_blank">awesome-vue</a>
      </li>
    </ul>

    <dataTime
      :visible="show"
      title="选择"
      :start="startTime"
      end="2030-09-30"
      v-model="result"
      @cancel="show=false"
    ></dataTime>
  </div>
</template>

<script>
import dataTime from "./dataTime.vue";
export default {
  name: "HelloWorld",
  components: { dataTime },
  data() {
    return {
      startTime: this.parseTime(),
      msg: "Welcome to Your Vue.js App",
      result: null,
      resultReverse: "11",
      show: false
    };
  },
  watch: {
    result() {
      let _result = this.result
      this.resultReverse = _result.split("-").reverse().join("-")
    }
  },
  methods: {
    parseTime(t = new Date()) {
      let _date = new Date(t),
        y = _date.getFullYear(),
        m = _date.getMonth() + 1,
        d = _date.getDate();
      return y + "-" + (m > 9 ? m : "0" + m) + "-" + (d > 9 ? d : "0" + d);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
