<template>
  <div class="hello"></div>
</template>

<script>
import {
  getAuthClient,
  initAuthClient,
  AuthingGuard as NativeAuthingGuard,
  GuardEventsCamelToKebabMap,
  GuardMode,
  GuardScenes,
  LoginMethods,
  RegisterMethods,
} from "@authing/native-js-ui-components";
import "@authing/native-js-ui-components/lib/index.min.css";

const callbackEvent = ["before-login", "before-register"];

export { getAuthClient, initAuthClient, GuardMode, GuardScenes, LoginMethods, RegisterMethods };

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
    appId: String,
    config: {
      type: Object,
      required: false,
      default () {
        return {}
      }
    },
    tenantId: {
      type: String,
      required: false,
      default () {
        return ''
      }
    },
    visible: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      localVisible: false,
      localConfig: this.config
    }
  },
  watch: {
    visible: {
      immediate: true,
      handler(val) {
        if (val !== this.localVisible) {
          this.localVisible = val;
        }
      },
    },
    localVisible: {
      handler(val) {
        if (val !== this.visible) {
          this.$emit("update:visible", val)
        }

        if (val) {
          this.show()
        } else {
          this.hide()
        }
      },
    },
  },
  mounted () {
    this.localConfig.mode = this.mode
    this.localConfig.autoRegister = this.autoRegister ? this.autoRegister : this.localConfig.autoRegister;
    this.localConfig.isSSO = this.isSSO ? this.isSSO : this.localConfig.isSSO;
    this.localConfig.clickCloseable = this.clickCloseable ? this.clickCloseable : this.localConfig.clickCloseable;
    this.localConfig.escCloseable = this.escCloseable ? this.escCloseable : this.localConfig.escCloseable;

    const guard = new NativeAuthingGuard(this.appId, this.localConfig, this.tenantId);

    console.log('guard: ', guard)

    const evts = Object.values(GuardEventsCamelToKebabMap);
    const kebabToCamelMap = Object.entries(GuardEventsCamelToKebabMap).reduce((acc, [camel, kebab]) => {
      return Object.assign({}, acc, {
        [kebab]: camel,
      });
    }, {});

    const listeners = evts.reduce((acc, evtName) => {
      return Object.assign({}, acc, {
        [evtName]: (...rest) => {
          if (evtName === "close") {
            this.localVisible = false;
          }
          if (!callbackEvent.includes(evtName)) {
            this.$emit(evtName, ...rest);
          } else {
            const camelEvtName = kebabToCamelMap[evtName];

            if (this[camelEvtName]) {
              return this[camelEvtName](...rest);
            }
            return true;
          }
        },
      });
    }, {});

    evts.forEach((evtName) => guard.on(evtName, listeners[evtName]));

    if (this.localVisible) {
      guard.show();
    }
    this.$guard = guard;
  },
  methods: {
    hide: function() {
      this.$guard.hide()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
