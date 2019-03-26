---
title: Rendering Kondisional
type: guide
order: 7
---

## `v-if`

Direktif `v-if` digunakan untuk menampilkan suatu blok ketika suatu kondisi terpenuhi (bernilai _true_).

``` html
<h1 v-if="awesome">Vue itu keren!</h1>
```

Kalian juga bisa menggunakan `v-else` untuk menampilkan blok ketika kondisi sebelumnya tidak terpenuhi.

``` html
<h1 v-if="awesome">Vue itu keren!</h1>
<h1 v-else>Oh no 😢</h1>
```

### Menggabungkan Kondisi dengan `v-if` di `<template>`

Karena `v-if` adalah direktif, maka hanya bisa dipasang di satu elemen. Bagaimana caranya jika ingin menampilkan lebih dari satu elemen ketika suatu kondisi terpenuhi? Kalian bisa membungkus elemen yang ingin ditampilkan di dalam elemen `<template>` yang diberi `v-if`. Elemen `<template>` tidak akan ikut serta ditampilkan karena bersifat sebagai pembungkus.

``` html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### `v-else`

Sama seperti contoh sebelumnya, kalian bisa menggunakan direktif `v-else` untuk menampilkan elemen ketika kondisi diatasnya bernilai _false_:

``` html
<div v-if="Math.random() > 0.5">
  Sekarang kelihatan
</div>
<div v-else>
  Sekarang tidak
</div>
```

Direktif `v-else` harus berada di bawah elemen yang berdirektif `v-if` atau `v-else-if` supaya bisa diproses oleh Vue.

### `v-else-if`

> Baru di versi 2.1.0+

Direktif `v-else-if` digunakan untuk memberikan kondisi alternatif lainnya ketika kondisi sebelumnya tidak terpenuhi, dan bisa digunakan berkali-kali:

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

Sama seperti `v-else`, direktif `v-else-if` harus berada di bawah elemen yang berdirektif `v-if` atau `v-else-if`.

### Mengatur Penggunaan Ulang Komponen dengan `key`

Vue akan mencoba menampilkan elemen seefisien mungkin, seperti menggunakan ulang elemen-elemen yang sudah ada dibanding membuatnya lagi dari awal. Selain membuat Vue bekerja lebih cepat, ada kelebihan lainnya. Contohnya, jika kalian membolehkan pengguna untuk masuk dengan beberapa jenis _login_:

``` html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

Ketika `loginType` di atas dirubah, Vue tidak akan menghapus data yang sudah dimasukan oleh pengguna. Karena kedua templat menggunakan elemen yang sama, `<input>` tidak akan ditindih, hanya nilai `placeholder`-nya saja.

Kalian bisa mencobanya sendiri dengan menuliskan beberapa teks, lalu menekan tombol _toggle_:

{% raw %}
<div id="no-key-example" class="demo">
  <div>
    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address">
    </template>
  </div>
  <button @click="toggleLoginType">Toggle login type</button>
</div>
<script>
new Vue({
  el: '#no-key-example',
  data: {
    loginType: 'username'
  },
  methods: {
    toggleLoginType: function () {
      return this.loginType = this.loginType === 'username' ? 'email' : 'username'
    }
  }
})
</script>
{% endraw %}

Kadang kita tidak membutuhkan efek seperti ini, karena itu Vue memberikan cara untuk menyatakan: "Kedua elemen ini berbeda, jangan digunakan ulang!"; dengan menambahkan atribut `key` dengan nilai yang berbeda/unik.

``` html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

Sekarang input diatas akan dirender ulang setiap kali nilai `loginType` berubah:

{% raw %}
<div id="key-example" class="demo">
  <div>
    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address" key="email-input">
    </template>
  </div>
  <button @click="toggleLoginType">Toggle login type</button>
</div>
<script>
new Vue({
  el: '#key-example',
  data: {
    loginType: 'username'
  },
  methods: {
    toggleLoginType: function () {
      return this.loginType = this.loginType === 'username' ? 'email' : 'username'
    }
  }
})
</script>
{% endraw %}

Catat bahwa elemen `<label>` masih digunakan ulang supaya lebih efisien, karena tidak diberi atribut `key`.

## `v-show`

Opsi lain untuk menampilkan elemen ketika suatu kondisi terpenuhi adalah menggunakan direktif `v-show`, caranya kurang lebih mirip:

``` html
<h1 v-show="ok">Hello!</h1>
```

Bedanya adalah elemen dengan direktif `v-show` akan selalu ditampilkan dan tinggal di DOM; karena `v-show` hanya merubah nilai properti CSS `display` dari elemen tersebut.

<p class="tip">Catat bahwa `v-show` tidak bisa digunakan di elemen `<template>`, dan tidak bisa digunakan dengan `v-else`.</p>

## `v-if` vs `v-show`

`v-if` adalah rendering kondisional yang "sesungguhnya" karena memastikan bahwa semua _event listener_ dan komponen di dalamnya akan dihapus dan dbuat ulang disetiap perubahan _toggle_.

`v-if` juga bersifat **_lazy_**: jika kondisinya tidak terpenuhi ketika pertama kali ditampilkan, tidak akan terjadi apa-apa - blok kondisionalnya tida  akan ditampilkan sampai kondisinya bernilai _true_.

Sedangkan`v-show` lebih sederhana, karena elemennya akan selalu ditampilkan, hanya saja nilai properti CSS `display`-nya saja yang dirubah.

Secara umum, `v-if` berat ketika terjadi peralihan kondisi, sedangkan `v-show` berat saat elemen pertama kali ditampilkan. Jadi gunakan `v-show` jika sering terjadi peralihan kondisi, dan gunakan `v-if` jika tidak sering.

## `v-if` with `v-for`

<p class="tip">Penggunaan `v-if` dan `v-for` secara bersamaan **tidak direkomendasikan**. Lihat [panduan gaya](/v2/style-guide/#Avoid-v-if-with-v-for-essential) untuk informasi lebih lanjut.</p>

Ketika digunakan secara bersamaan dengan `v-if`, `v-for` punya prioritas yang lebih tinggi dibandingkan `v-if`. Lihat <a href="../guide/list.html#V-for-and-v-if">panduan menampilkan daftar/_list_</a> untuk detailnya.
