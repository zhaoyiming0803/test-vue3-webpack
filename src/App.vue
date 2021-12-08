<template>
  <div>
    <!--<WrapperedAuthingGuard />-->
    <AuthingGuard :appId="appId" @login="onLogin" :config="authingConfig" />
    <!--<HelloWorld :appId="appId" @login="onLogin" :config="authingConfig" />-->
    <!--<Abc />-->
    <TestComponent />
  </div>
</template>

<script>
// import { AuthingGuard } from '@authing/vue-ui-components'
import { AuthingGuard } from '@authing/vue-ui-components/src/components/index.js'
import '@authing/vue-ui-components/lib/index.min.css'
import { h } from 'vue'

console.log('AuthingGuard: ', AuthingGuard)

const TestComponent = {
  render () {
    return h('div', {}, this.c)
  },
  data () {
    return {
      a: 1,
      b: 2
    }
  },
  computed: {
    c() {
      return this.a + this.b
    }
  },
  created () {
    this._self = {
      _c: h
    }
  },
  mounted () {
    console.log('ccccccccc: ', this.c)
  }
}

// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    TestComponent,
    AuthingGuard,
    // HelloWorld,
    // Abc: h('div', {
    //   background: 'blue'
    // }, 'hello abc')
  },
  data () {
    return {
      appId: '61ada53ae69e78d80e31d3e4',
      authingConfig: {
        apiHost: 'https://lb68p7-demo.authing.cn'
      }
    }
  },
  mounted () {
    console.log('----------: ', this)
  },
  methods: {
    onLogin (userInfo) {
      console.log('onLogin: ', userInfo)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
