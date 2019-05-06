---
title: Komponen Satu Berkas
type: guide
order: 401
---

## Pengenalan

Di banyak proyek-proyek Vue, komponen-komponen global akan didefinisikan menggunakan `Vue.component`, diikuti oleh `new Vue({ el: '#container' })` untuk ditujukan ke sebuah elemen penampung pada badan di setiap halaman.

Ini dapat bekerja dengan sangat baik untuk proyek yang berukuran kecil hingga sedang, ketika JavaScript hanya digunakan untuk meningkatkan beberapa tampilan saja. Akan tetapi pada proyek yang lebih rumit, atau ketika _frontend_ kalian adalah halaman yang sangat ditentukan oleh JavaScript, kerugian berikut menjadi terlihat:

- **Definisi Global** memaksa kita untuk membuat nama yang unik pada setiap komponen.
- **Templat String** itu memiliki kekurangan pada peyorotan sintaks dan memerlukan banyak garis miring yang nampak tidak bagus pada _multiline HTML_
- **Tidak ada dukungan CSS** berarti sementara HTML dan JavaScript termodulasi menjadi komponen-komponen, CSS secara mencolok akan ditinggalkan
- **Tidak ada langkah pembangun** membatasi kita untuk menggunakan HTML dan ES5 Javascript, daripada menggunakan _preprocessors_ sepertu Pug (sebelumnya Jade) dan Babel

Semua permasalahan ini akan diselesaikan oleh **Komponen Satu Berkas** dengan menggunakan ekstensi `.vue`, membuatnya memungkinkan dengan menggunakan perangkat pembangun seperti Webpack atau Browserify.

Berikut merupakan contih dari sebuah berkas yang kita namakan `Hello.vue`:

<a href="https://gist.github.com/chrisvfritz/e2b6a6110e0829d78fa4aedf7cf6b235" target="_blank" rel="noopener noreferrer"><img src="/images/vue-component.png" alt="Contoh komponen satu berkas (klik untuk melihat kode sebagai teks)" style="display: block; margin: 30px auto;"></a>

Sekarang kita mendapatkan:

- [Penyorotan sintaks secara lengkap](https://github.com/vuejs/awesome-vue#source-code-editing)
- [Modul CommonJS](https://webpack.js.org/concepts/modules/#what-is-a-webpack-module)
- [Komponen-yang-dilingkupi CSS](https://vue-loader.vuejs.org/en/features/scoped-css.html)

Seperti yang dijanjikan, kita juga dapat menggunakan _preprocessors_ seperti Pug, Babel (dengan modul ES2015), dan Stylus agar komponen lebih bersih dan lebih kaya fitur.

<a href="https://gist.github.com/chrisvfritz/1c9f2daea9bc078dcb47e9a82e5f7587" target="_blank" rel="noopener noreferrer"><img src="/images/vue-component-with-preprocessors.png" alt="Contoh komponen satu berkas dengan menggunakan preprocessor (klik untuk melihat kode sebagai teks)" style="display: block; margin: 30px auto;"></a>

Bahasa yang spesifik tersebut hanya sebagai contoh. Kalian bisa dengan mudah menggunakan Bublé, TypeScript, SCSS, PostCSS atau _preprocessors_ apapun lainnya yang membantu kalian untuk tetap produktif. Jika menggunakan Webpack dengan `vue-loader`, itu juga memiliki dukungan terbaik untuk modul-modul CSS.

### Bagaimana Tentang Pemisahan Informasi Program?

Satu hal penting yang perlu diingat bahwa **Pemisahan informasi program tidak sama dengan memisahkan tipe-tipe berkas.** Pada pembangunan UI secara modern, kita telah menemukan bahwa daripada memisahkan basis kode menjadi tiga lapisan besar yang terjalin antara satu dengan lainnya, akan lebih terasa masuk akal jika dibagi menjadi komponen yang tidak-saling-terikat dan menyusun komponen tersebut. Di dalam komponen, templat, logika dan _styles_ secara inheren terikat, dan menyatukannya akan membuat komponen lebih berpadu dan mudah dipelihara.

Bahkan jika kalian tidak menyukai ide dari Komponen Satu Berkas, kalian masih dapat tetap menggunakan `hot-reloading` dan fitur _pre-compilation_ dengan memisahkan JavaScript dan CSS menjadi berkas terpisah:

``` html
<!-- my-component.vue -->
<template>
  <div>ini akan menjadi pre-compiled</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```

## Memulai

### Contoh Sandbox

Jika kalian ingin lebih dalam dan mulai bermain dengan komponen satu berkas, lihatlah [aplikasi todo sederhana ini](https://codesandbox.io/s/o29j95wx9) pada CodeSandbox.

### Untuk Pengguna Baru Sistem Modul Pembangun pada JavaScript

Dengan komponen-komponen `.vue`, kita akan memasuki dunia aplikasi JavaScript tingkat lanjut. Itu berarti kalian akan mempelajari menggunakan beberapa perangkat tambahan yang jika belum kalian miliki:

- **Node Package Manager (NPM)**: Baca [Panduan Memulai](https://docs.npmjs.com/getting-started/what-is-npm) sampai bagian _10: Mencopot pemasangan paket global_.

- **JavaScript Modern dengan ES2015/16**: Baca melalui [Mempelajari panduan ES2015](https://babeljs.io/docs/learn-es2015/) milik Babel. Kalian tidak perlu mengingat setiap fiturnya untuk saat ini, tapi jadikan laman tersebut sebagai referensi yang dapat menjadi rujukan kalian.

Setelah kalian menghabiskan waktu untuk mendalami sumber-sumber di atas, kami merekomendasikan untuk melihat [Vue CLI 3](https://cli.vuejs.org/). Ikuti petunjuk-petunjuknya dan kalian seharusnya memiliki sebuah proyek Vue dengan komponen-komponen `.vue`, ES2015, Webpack dan _hot-reloading_ dalam waktu singkat

<!-- ### For Advanced Users -->
### Bagi Pengguna Tingkat Lanjut

<!-- The CLI takes care of most of the tooling configurations for you, but also allows fine-grained customization through its own [config options](https://cli.vuejs.org/config/). -->
CLI akan menjadi sebagian besar perangkat pengaturan untuk kalian, tapi juga memungkinkan untuk dilakukan kustomisasi sesuai kebutuhan melalui [pilihan konfigurasi](https://cli.vuejs.org/config/).

<!-- In case you prefer setting up your own build setup from scratch, you will need to manually configure webpack with [vue-loader](https://vue-loader.vuejs.org). To learn more about webpack itself, check out [their official docs](https://webpack.js.org/configuration/) and [Webpack Academy](https://webpack.academy/p/the-core-concepts). -->
Pada kasus kalian lebih memilih untuk memasang pengaturan perangkat pembangun sendiri dari awal, kalian akan membutuhkan konfigurasi Webpack dengan [vue-loader](https://vue-loader.vuejs.org) secara manual. Untuk mempelajari lebih lanjut tentang Webpack itu sendiri, lihatlah [dokumentasi resminya](https://webpack.js.org/configuration/) dan [Akademi Webpack](https://webpack.academy/p/the-core-concepts).
