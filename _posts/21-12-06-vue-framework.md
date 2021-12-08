---
title: "[Ubuntu] vim 세팅"
tags: ubuntu env 
---

<!--more-->

# Vuetify

```sh
vue add vuetify
```

## Vue-Bootstrap

```sh
npm install vue bootstrap-vue bootstrap
```

### `main.js`

```js
import Vue from 'vue'
import App from './App.vue'
import BootstrapVue from 'bootstrap-vue'
import 'bootstrap/dist/css/bootstrap.min.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

Vue.use(BootstrapVue)

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')
```