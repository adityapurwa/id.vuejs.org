---
title: Mixin
type: guide
order: 301
---

## Dasar-dasar Mixin

Mixin adalah cara fleksibel untuk mendistribusikan fungsionalitas komponen Vue yang bisa digunakan berulang kali. Sebuah objek mxin bisa berisi opsi-opsi komponen. Ketika komponen menggunakan sebuah mixin, semua opsi-opsi yang ada di mixin tersebut akan di "mix" (campur) dengan opsi-opsi komponen itu sendiri.


<div class="vue-mastery"><a href="https://www.vuemastery.com/courses/next-level-vue/mixins" target="_blank" rel="noopener" title="Mixins Tutorial">Lihat penjelasan di Vue Mastery</a></div>

Contoh:

``` js
// buat objek mixin
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

// buat komponen yang memanggil mixin di atas
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "hello from mixin!"
```

## Penggabungan Opsi

Ketika ada opsi dari mixin dan komponen yang saling bentrok, mereka akan digabungkan dengan beberapa cara.

Contoh, opsi data akan digabungkan secara rekursif, dengan data komponen yang akan menindih data mixin ketika terjadi bentrokan.

``` js
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```

Fungsi _hook_ dengan nama yang sama juga akan digabungkan menjadi sebuah array yang masing-masing akan dipanggil. Hook dari mixin akan dipanggil **sebelum** hook komponen itu sendiri.

``` js
var mixin = {
  created: function () {
    console.log('mixin hook called')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('component hook called')
  }
})

// => "mixin hook called"
// => "component hook called"
```

Opsi yang menerima nilai dalam bentuk objek, contoh `methods`, `components`, dan `directivces`, akan digabungkan menjadi satu objek, di mana opsi dari komponen tersebut akan menindih opsi yang ada di mixin ketika ada _keys_ yang sama di objek tersebut:

``` js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```

Ingat, strategi penggabungan yang sama juga digunakan di `Vue.extend()`.
Note that the same merge strategies are used in `Vue.extend()`.

## Mixin Global

Kalian bisa memanggil mixin secara global. Gunakan dengan hati-hati! Ketika mixin dipanggil secara global, mixin tersebut akan diterapkan di **semua** instan Vue yang akan dibuat. Ketika digunakan dengan benar, kita bisa memproses opsi kustom melalui mixin:

``` js
// sisipkan pemroses untuk opsi `myOption`
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// => "hello!"
```

<p class="tip">
Gunakan mixin global dengan bijak, karena mixin ini akan diterapkan di semua instan Vue yang akan di buat, termasuk komponen pihak ketiga. Umumnya, kalian hanya akan menggunakannya untuk memproses opsi kustom seperti contoh di atas. Mixin Global lebih baik didistribusikan sebagai [Plugin](plugins.html) untuk menghindari duplikasi.</p>

## Strategi Penggabungan Opsi Kustom

Ketika opsi kustom akan digabungkan, mereka menggunakan strategi penggabungan yang akan menindih nilai yang sudah ada. Jika kalian ingin merubah proses penggabungan ini, kalian bisa membuat fungsi `Vue.config.optionsMergeStrategies`:

``` js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}
```

Umumnya, untuk opsi bernilai objek, kalian bisa menggunakan strategi yang digunakan untuk `methods`:

``` js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

Contoh yang lebih lengkap bisa dilihat di strategi penggabungan [Vuex](https://github.com/vuejs/vuex) 1.x:

``` js
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```
