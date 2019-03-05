---
title: Routing
type: guide
order: 501
---

## Router Resmi

Untuk kebanyakan Aplikasi Satu Halaman, sangat direkomendasikan untuk menggunakan [library vue-router](https://github.com/vuejs/vue-router) yang didukung resmi. Untuk rincian lebih lanjut, lihat [dokumentasi](https://router.vuejs.org/) vue-router.

## Routing sederhana dari nol

Jika kalian hanya membutuhkan _routing_ yang sangat sederhana dan tidak ingin mengikut sertakan library router berfitur lengkap, kalian bisa melakukannya dengan cara me-_render_ komponen _page-level_ secara dinamis seperti berikut:

``` js
const NotFound = { template: '<p>Page not found</p>' }
const Home = { template: '<p>home page</p>' }
const About = { template: '<p>about page</p>' }

const routes = {
  '/': Home,
  '/about': About
}

new Vue({
  el: '#app',
  data: {
    currentRoute: window.location.pathname
  },
  computed: {
    ViewComponent () {
      return routes[this.currentRoute] || NotFound
    }
  },
  render (h) { return h(this.ViewComponent) }
})
```

Digabung dengan API Sejarah HTML5, kalian bisa membangun router _client-side_ yang sangat mendasar tapi berfungsi sepenuhnya. Untuk melihatnya dalam praktek, bisa kalian cek di [contoh aplikasi ini](https://github.com/chrisvfritz/vue-2.0-simple-routing-example).

## Mengintegrasi Router Pihak Ketiga

Jika ada router pihak ketiga yang ingin kalian gunakan, seperti [Page.js](https://github.com/visionmedia/page.js) atau [Director](https://github.com/flatiron/director), mengintegrasi merupakan hal yang [cukup mudah](https://github.com/chrisvfritz/vue-2.0-simple-routing-example/compare/master...pagejs). Berikut adalah [contoh lengkap](https://github.com/chrisvfritz/vue-2.0-simple-routing-example/tree/pagejs) dengan menggunakan Page.js.
