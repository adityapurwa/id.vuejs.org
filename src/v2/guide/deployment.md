---
title: Peluncuran Produksi
type: guide
order: 404
---

> Sebagian besar tips dibawah sudah terkonfigurasi jika kalian menggunakan [Vue CLI](https://cli.vuejs.org). Bagian panduan ini hanya ditujukan untuk kalian yang menggunakan proses build yang kalian kustomisasi sendiri.

## Mengaktifkan Mode Produsi

Dalam masa pengembangan, Vue menyediakan banyak sekali pesan peringatan untuk menghindari kesalahan yang umum terjadi. Tetapi, pesan peringatan ini tidak bermanfaat di produksi dan hanya membuat ukuran aplikasi kalian membengkak. Juga, beberapa peringatan ini melakukan proses pengecekan yang bisa membuat jalannya aplikasi kalian berjalan lebih lambat.

### Tanpa _Build Tool_

Jika kalian menggunakan edisi _full build_, seperti menggunakan Vue langsung melalui _script_ tag tanpa menggunakan build tool, pastikan kalian menggunakan versi yang sudah dikecilkan (`vue.min.js`) untuk lingkungan produksi kalian. Ada dua versi yang bisa kalian cek di [Installation guide](installation.html#Direct-lt-script-gt-Include).

### Dengan Build Tool

Ketika kalian menggunakan build tool seperti Webpack atau Browserify, mode produksi akan diaktifkan berdasarkan nilai dari `process.env.NODE_ENV` di dalam sumber kode Vue. Pada dasarnya, Vue akan menggunakan mode development. Kedua build tool ini menyediakan cara untuk menindih nilai variabel ini sehingga Vue bisa berjalan di mode produksi, dan pesan peringatan akan dihapus dari hasil build. Semua templat `vue-cli` sudah terkonfigurasi dan siap digunakan, berikut cara kerjanya:

#### Webpack

Di Webpack 4+, kalian bisa menggunakan opsi `mode`:

```js
module.exports = {
  mode: "production"
};
```

Tetapi di Webpack 3 kebawah, kalian harus meggunakan [DefinePlugin](https://webpack.js.org/plugins/define-plugin/):

```js
var webpack = require("webpack");

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      "process.env.NODE_ENV": JSON.stringify("production")
    })
  ]
};
```

#### Browserify

- Jalankan perintah bundling dengan variabel lingkungan `NODE_ENV` diset ke `"production"`. Dengan ini `vueify` tidak akan mengikut sertakan kode untuk `hot-reload` dan development.

- Terapkan transformasi [envify](https://github.com/hughsk/envify) ke bundle kalian. Dengan ini minifier akan menghilangkan semua peringatan di Vue. Contoh:

  ```bash
  NODE_ENV=production browserify -g envify -e main.js | uglifyjs -c -m > build.js
  ```

- Atau, gunakan [envify](https://github.com/hughsk/envify) dengan Gulp:

  ```js
  // Gunakan modul kustom envify untuk menentukan nilai variabel lingkungan
  var envify = require("envify/custom");

  browserify(browserifyOptions)
    .transform(vueify)
    .transform(
      // Penting untuk memproses berkas-berkas yang ada di node_modules
      { global: true },
      envify({ NODE_ENV: "production" })
    )
    .bundle();
  ```

- Atau, gunakan [envify](https://github.com/hughsk/envify) dengan Grunt dan [grunt-browserify](https://github.com/jmreidy/grunt-browserify):

  ```js
  // Gunakan modul kustom envify untuk menentukan nilai variabel lingkungan
  var envify = require("envify/custom");

  browserify: {
    dist: {
      options: {
        // Fungsi untuk merubah urutan grunt-browserify
        configure: b =>
          b
            .transform("vueify")
            .transform(
              // Penting untuk memproses berkas-berkas yang ada di node_modules
              { global: true },
              envify({ NODE_ENV: "production" })
            )
            .bundle();
      }
    }
  }
  ```

#### Rollup

Gunakan [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace):

```js
const replace = require('rollup-plugin-replace')

rollup({
  // ...
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify( 'production' )
    })
  ]
}).then(...)
```

## Prekompilasi Templat

Ketika menggnuakan templat _in-DOM_ atau templat string di dalam JavaScript, proses kompilasi template-to-render-function dilakukan saat aplikasi berjalan. Proses ini sudah lumayan cepat, tapi lebih baik dihindari jika aplikasi kalian butuh performa yang maksimal.

Cara paling mudah untuk pre-kompilasi templat adalah menggunakan [Komponen Satu Berkas / _Single-File Component_](single-file-components.html) - proses build akan langsung melakukan proses prekompilasi untuk kalian, sehingga kode yang dihasilkan sudah dalam bentuk fungsi render yang terkompilasi dan tidak dalam bentuk templat string mentah.

Jika kalian menggunakan Webpack, dan lebih memilih memisahkan JavaScript dengan berkas templat, kalian bisa menggunakan [vue-template-loader](https://github.com/ktsn/vue-template-loader), yang akan merubah berkas templat menjadi fungsi render JavaScript di saat proses build berjalan.

## Mengekstrak CSS dari Komponen

Ketika menggunakan Komponen Satu Berkas / Single-File Component, CSS di dalam komponen akan dimasukkan secara dinamis ke dalam tag `<style>`. Proses ini bisa memperlambat jalannya aplikasi, dan jika kalian menggunakan perenderan di sisi server akan terjadi _"flash of unstyled content"_ (waktu di mana secara singkat komponen tampil berantakan). Dengan mengekstrak CSS dari komponen ke satu berkas, kita bisa menghindari isu-isu di atas, dan juga kita bisa memanfaatkan proses minifikasi CSS dan _caching_.
Lihat dokumentasi build tool masing-masing untuk melihat bagaimana caranya:

- [Webpack + vue-loader](https://vue-loader.vuejs.org/en/configurations/extract-css.html) (templat webpack `vue-cli` sudah terkonfigurasi)
- [Browserify + vueify](https://github.com/vuejs/vueify#css-extraction)
- [Rollup + rollup-plugin-vue](https://vuejs.github.io/rollup-plugin-vue/#/en/2.3/?id=custom-handler)

## Melacak Error Saat Aplikasi Berjalan

Jika terjadi error saat aplikasi melakukan proses render komponen, error tersebut akan diteruskan ke konfigurasi fungsi global `Vue.config.errorHandler` jika konfigurasinya sudah diatur. Kalian bisa menggunakan _hook_ error ini dengan layanan pelacak error seperti [Sentry](https://sentry.io), yang juga menyediakan [integrasi resmi](https://sentry.io/for/vue/) dengan Vue.
