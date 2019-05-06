---
title: Dukungan TypeScript
type: guide
order: 403
---

> [Vue CLI](https://cli.vuejs.org) menyediakan dukungan perangkat TypeScript. Pada versi mayor Vue (3.x) selanjurnya, kami juga berencana untuk meningkatkan dukungan TypeScript dengan komponen API berbasis _class_ yang sudah terintegrasi dan dukungan TSX.

## Deklarasi Resmi pada Paket-Paket NPM

Sistem penulisan statis dapat membantu mencegah banyak potensi kesalahan pada saat _runtime_, terutama saat aplikasi berkembang. Itulah mengapa Vue hadir dengan [deklarasi penulisan statis](https://github.com/vuejs/vue/tree/dev/types) untuk [TypeScript](https://www.typescriptlang.org/) - tidak hanya dalam inti Vue, melainkan juga pada [vue-router](https://github.com/vuejs/vue-router/tree/dev/types) dan [vuex](https://github.com/vuejs/vuex/tree/dev/types) juga.

Karena sistem ini [dipublikasikan di NPM](https://cdn.jsdelivr.net/npm/vue/types/), dan TypeScript terkini bisa mengetahui bagaimana cara menyelesaikan deklarasi tipe dalam paket-paket NPM, ini berarti ketika melakukan pemasangan melalui NPM, kita tidak memerlukan perangkat tambahan lagi untuk menggunakan TypeScript bersama Vue.

## Konfigurasi yang Dianjurkan

``` js
// tsconfig.json
{
  "compilerOptions": {
    // ini sesuai dengan dukungan peramban Vue
    "target": "es5",
    // ini mengaktifkan inferensi yang lebih ketat untuk properti data pada `this`
    "strict": true,
    // jika menggunakan webpack 2+ atau rollup, untuk memanfaatkan tree shaking
    "module": "es2015",
    "moduleResolution": "node"
  }
}
```

Perlu diingat bahwa kalian harus menyertakan `strict: true` (atau setidaknya `noImplicitThis: true` yang mana merupakan bagian dari tanda `strict`) untuk memanfaatkan pengecekan tipe dari `this` dalam fungsi komponen jika tidak itu akan selalu diberlakukan sebagai tipe `apapun`.

Lihat [dokumentasi pilihan kompiler TypeScript](https://www.typescriptlang.org/docs/handbook/compiler-options.html) untuk lebih detail.

## Perangkat Pengembangan

### Pembuatan Proyek

[Vue CLI 3](https://github.com/vuejs/vue-cli) dapat menghasilkan proyek baru yang menggunakan TypeScript. Untuk memulai:

```bash
# 1. Pasang Vue CLI, jika belum terpasang
npm install --global @vue/cli

# 2. Buat proyeck baru, lalu pilih "Manually select features"
vue create my-project-name
```

### Dukungan Editor

Untuk mengembangkan aplikasi Vue dengan TypeScript, kami sangat merekomendasikan menggunakan [Visual Studio Code](https://code.visualstudio.com/), yang banyak menyediakan dukungan bagus untuk TypeScript. Jika kalian menggunakan [Komponen satu berkas](./single-file-components.html) (SFCs), dapatkan [ekstensi Vetur](https://github.com/vuejs/vetur) yang luar biasa, menyediakan inferensi TypeScript di dalam SFCs dan banyak fitur menarik lainnya.

[Webstorm](https://www.jetbrains.com/webstorm/) juga menyediakan dukungan lengkap untuk TypeScript dan Vue.

## Penggunaan Dasar

Agar TypeScript dapat menebak tipe di dalam pilihan komponen Vue, kalian perlu untuk mendefinisikan komponen-komponen `Vue.component` atau `Vue.extend`:

``` ts
import Vue from 'vue'

const Component = Vue.extend({
  // type inference diaktifkan
})

const Component = {
  // ini tidak akan memiliki type inference
  // karena TypeScript tidak dapat menebak pilihan ini merupakan sebuah komponen Vue/
}
```

## Komponen Class-Style Vue

Jika kalian lebih memilih sebuah _class-based_ API ketika mendeklarasikan komponen-komponen, kalian dapat menggunakan dekorator yang dikelola secara resmi [vue-class-component](https://github.com/vuejs/vue-class-component):

``` ts
import Vue from 'vue'
import Component from 'vue-class-component'

// dekorator @Component mengindikasikan class merupakan sebuah komponen Vue
@Component({
  // Semua pilihan komponen diperbolehkan di sini
  template: '<button @click="onClick">Click!</button>'
})
export default class MyComponent extends Vue {
  // data awal dapat dideklarasikan sebagai properti instan
  message: string = 'Hello!'

  // fungsi komponen dapat dideklarasikan sebagai fungsi instan
  onClick (): void {
    window.alert(this.message)
  }
}
```

## Augmentasi Tipe untuk Penggunaan dengan Plugin

Plugin bisa ditambahkan ke dalam properti instan global dan pilihan komponen. Pada kasus ini, deklarasi tipe dibutuhkan untuk melakukan kompilasi plugin dalam TypeScript. Untunglah, sudah ada fitur TypeScript untuk augmentasi tipe yang dinamakan [module augmentation](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation).

Contohnya, untuk mendeklarasikan properti instan `$myProperty` dengan tipe `string`:

``` ts
// 1. Pastikan untuk melakukan import `vue` sebelum mendeklarasikan tipe-tipe yang diaugmentasi
import Vue from 'vue'

// 2. spesifikasikan sebuah berkas dengan tipe yang ingin kalian augmentasikan
//    Vue memiliki konstruktor tipe pada types/vue.d.ts
declare module 'vue/types/vue' {
  // 3. Deklarasikan augmentasi untuk Vue
  interface Vue {
    $myProperty: string
  }
}
```

Setelah menyertakan kode di atas sebagai sebuah berkas deklarasi (seperti `my-property.d.ts`) ke dalam proyek kalian, kalian bisa menggunaan `$myProperty` pada sebuah instan Vue.

```ts
var vm = new Vue()
console.log(vm.$myProperty) // Ini harusnya dapat dikompilasi dengan sukses
```

Kalian juga dapat mendeklarasikan properti global tambahan dan pilihan komponen:

```ts
import Vue from 'vue'

declare module 'vue/types/vue' {
  // Properti global dapat dideklarasikan
  // pada interface `VueConstructor`
  interface VueConstructor {
    $myGlobal: string
  }
}

// ComponentOptions dideklarasikan dalam types/options.d.ts
declare module 'vue/types/options' {
  interface ComponentOptions<V extends Vue> {
    myOption?: string
  }
}
```

Deklarasi di atas memungkinkan kode disusun sebagai berikut:

```ts
// Properti global
console.log(Vue.$myGlobal)

// opsi komponen tambahan
var vm = new Vue({
  myOption: 'Hello'
})
```

## Anotasi Pengembalian Tipe

Karena sudah sifat alami dari pendeklarasian berkas Vue, TypeScript mungkin mengalami kesulitan menyimpulkan tipe dari fungsi tertentu. Untuk alasan ini, kalian mungkin butuh untuk menambahkan keterangan kembalian tipe pada fungsi seperti `render` dan semua yang terlibat di dalam `computed`.

```ts
import Vue, { VNode } from 'vue'

const Component = Vue.extend({
  data () {
    return {
      msg: 'Hello'
    }
  },
  methods: {
    // membutuhkan anotasi karena `this` pada pengembalian tipe
    greet (): string {
      return this.msg + ' world'
    }
  },
  computed: {
    // membutuhkan anotasi
    greeting(): string {
      return this.greet() + '!'
    }
  },
  // `createElement` telah disimpulkan, tapi `render` membutuhkan kembalian tipe
  render (createElement): VNode {
    return createElement('div', this.greeting)
  }
})
```

Jika kalian menemukan hasil simpulan tipe atau penyelesaian anggota tidak berjalan, melakukan anotasi pada fungsi tertentu bisa dapat membantu mengatasi masalah ini. Menggunakan opsi `--noImplicitAny` akan membantu menemukan banyak fungsi yang belum dianotasikan.
