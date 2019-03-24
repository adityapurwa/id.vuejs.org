---
title: Unit Testing
type: guide
order: 402
---

> [Vue CLI](https://cli.vuejs.org/) memiliki pilihan _built-in_ untuk unit testing dengan [Jest](https://github.com/facebook/jest) atau [Mocha](https://mochajs.org/) yang dapat langsung digunakan. Kami juga memiliki [Vue Test Utils](https://vue-test-utils.vuejs.org/) resmi yang menyediakan panduan lebih rinci untuk pengaturan custom.

## Melakukan Assertion Sederhana

Kalian tidak perlu melakukan sesuatu yang spesial pada komponen agar dapat dilakukan pengujian. Ekspor opsi _raw_:

``` html
<template>
  <span>{{ message }}</span>
</template>

<script>
  export default {
    data () {
      return {
        message: 'hello!'
      }
    },
    created () {
      this.message = 'bye!'
    }
  }
</script>
```

Lalu impor opsi komponen bersama Vue, dan kalian dapat membuat banyak _assertion_ umum (di sini kita menggunakan model _assertion_ `expect` Jasmine/Jest sebagai contoh):

``` js
// Impor Vue dan komponen yang akan dites
import Vue from 'vue'
import MyComponent from 'path/to/MyComponent.vue'

// Di sini adalah beberapa test Jasmine 2.0, meskipun kalian bisa
// menggunakan runner test / pustaka assertion apapun yang kalian inginkan
describe('MyComponent', () => {
  // Periksa opsi komponen raw
  it('has a created hook', () => {
    expect(typeof MyComponent.created).toBe('function')
  })

  // Evaluasi hasil dari fungsi pada
  // opsi komponen raw
  it('sets the correct default data', () => {
    expect(typeof MyComponent.data).toBe('function')
    const defaultData = MyComponent.data()
    expect(defaultData.message).toBe('hello!')
  })

  // Periksa instan komponen pada mount
  it('correctly sets the message when created', () => {
    const vm = new Vue(MyComponent).$mount()
    expect(vm.message).toBe('bye!')
  })

  // Muat sebuah instan dan periksa keluaran render
  it('renders the correct message', () => {
    const Constructor = Vue.extend(MyComponent)
    const vm = new Constructor().$mount()
    expect(vm.$el.textContent).toBe('bye!')
  })
})
```

## Menulis Komponen yang Dapat Diuji

Keluaran render dari sebuah komponen pada dasarnya ditentukan oleh _props_ yang diterima. Jika keluaran render komponen hanya bergantung pada _props_ itu sendiri maka menjadi mudah untuk dilakukan pengujian, sama halnya pada proses _assertion_ kembalian nilai dari fungsi dengan argumen yang berbeda. Ambil contoh yang sederhana seperti berikut:

``` html
<template>
  <p>{{ msg }}</p>
</template>

<script>
  export default {
    props: ['msg']
  }
</script>
```

Kalian dapat melakukan _assert_ keluaran render dengan props yang berbeda menggunakan opsi `propsData`:

``` js
import Vue from 'vue'
import MyComponent from './MyComponent.vue'

// fungsi bantuan yang memuat dan mengembalikan teks yang sudah di-render
function getRenderedText (Component, propsData) {
  const Constructor = Vue.extend(Component)
  const vm = new Constructor({ propsData: propsData }).$mount()
  return vm.$el.textContent
}

describe('MyComponent', () => {
  it('renders correctly with different props', () => {
    expect(getRenderedText(MyComponent, {
      msg: 'Hello'
    })).toBe('Hello')

    expect(getRenderedText(MyComponent, {
      msg: 'Bye'
    })).toBe('Bye')
  })
})
```

## Melakukan Assertion Pada Pembaruan Asynchronous

Sejak Vue [melakukan pembaruan DOM secara asynchronous](reactivity.html#Async-Update-Queue), melakukan _assertion_ pada pembaruan DOM akibat dari perubahan state harus dibuat melalui callback `Vue.nextTick`:

``` js
// Periksa HTML yang diciptakan setelah pembaruan state
it('updates the rendered message when vm.message updates', done => {
  const vm = new Vue(MyComponent).$mount()
  vm.message = 'foo'

  // tunggu "tick" setelah perubahan state sebelum melakukan assertion pembaruan DOM
  Vue.nextTick(() => {
    expect(vm.$el.textContent).toBe('foo')
    done()
  })
})
```

Untuk mendapatkan informasi yang lebih dalam dan rinci mengenai unit testing pada Vue, lihat [Vue Test Utils](https://vue-test-utils.vuejs.org/) dan cookbook kami tentang [pengajuan unit komponen Vue](https://vuejs.org/v2/cookbook/unit-testing-vue-components.html)
