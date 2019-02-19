---
title: Instan Vue
type: guide
order: 3
---

## Membuat Instan Vue

Setiap aplikasi Vue dimulai dengan membuat **instan Vue** baru dengan fungsi `Vue`:

```js
var vm = new Vue({
  // pilihan
})
```

Meskipun tidak terkait erat dengan [pola MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), sebagian desain Vue terinspirasi dari pola MVVM. Sebagai konvensi, kami sering menggunakan variabel `vm` (kependekan dari ViewModel) untuk merujuk ke sebuah instan Vue.

Saat kalian membuat instance Vue, kalian mengirimkan **objek opsi (options object)**. Sebagian besar isi dari panduan ini menjelaskan bagaimana kalian dapat menggunakan objek opsi ini untuk merubah perilaku dari instan Vue kalian. Sebagai bahan referensi, kalian juga dapat menelusuri daftar opsi yang lebih lengkap di [referensi API](../api/#Options-Data).

Aplikasi Vue terdiri dari **instan Vue utama (root Vue Instance)** yang dibuat dengan `new Vue`, yang bisa ditata menjadi seperti sebuah pohon yang terdiri dari komponen-komponen yang bisa digunakan kembali (_reusable component_). Contoh, bentuk pohon komponen dari aplikasi todo akan terlihat seperti ini:

```
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

Kita akan membahas tentang [sistem komponen](components.html) lebih rinci nanti. Untuk saat ini, perlu diketahui bahwa semua komponen Vue adalah sebuah instan Vue, sehingga bisa diberi parameter opsi juga (kecuali opsi-opsi yang memang hanya tersedia untuk komponen utama).

## Data dan Method

Ketika instan Vue dibuat, instan tersebut akan membaca properti dari objek `data` ke **sistem reaktivitas** Vue. Ketika nilai properti tersebut berubah, tampilan akan "bereaksi", berubah menjadi nilai properti yang baru.

```js
// data objek kalian
var data = { a: 1 }

// Sebuah objek telah ditambahkan ke instan Vue
var vm = new Vue({
  data: data
})

// Mengambil properti dari instan
// Mengembalikan data sebelumnya
vm.a == data.a // => true

// Merubah sebuah properti dari instan
// juga akan merubah data asli sebelumnya
vm.a = 2
data.a // => 2

// ... dan sebaliknya
data.a = 3
vm.a // => 3
```

Ketika data ini berubah, tampilan akan dirender ulang. Perlu dicatat bahwa properti di `data` hanya akan **reaktif** jika properti tersebut didefinisikan saat instan tersebut dibuat. Jika kalian menambahkan properti baru, seperti:

```js
vm.b = 'hai'
```

Maka perubahan `b` tidak akan memicu pembaruan tampilan apa pun. Jika kalian butuh properti tersebut, maka kalian harus mendefinisikannya terlebih dahulu dengan nilai awal. Sebagai contoh:

```js
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

Satu-satunya pengecualian untuk hal ini adalah penggunaan `Object.freeze()`, yang mencegah perubahan suatu properti, dengan menggunakan `Object.freeze()`, sistem reaktifitas Vue tidak akan bisa mendeteksi perubahan.

```js
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
```

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- ini tidak akan diperbarui `foo`! -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```

Selain properti data, instan Vue juga memiliki sejumlah properti instan dan metode lainnya. Properti dan metode ini diawali dengan huruf `$` untuk membedakannya dari properti yang telah ditentukan pengguna. Sebagai contoh:

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch adalah sebuah metode instan
vm.$watch('a', function (newValue, oldValue) {
  // Callback ini akan di panggil ketika `vm.a` berubah
})
```

Kalian dapat melihat [referensi API](../api/#Instance-Properties) untuk daftar lengkap properti dan metode yang tersedia dari sebuah instan.

## Instan Lifecycle Hooks

Setiap instan Vue melewati serangkaian langkah inisialisasi saat dibuat - misalnya, Vue perlu mengatur observasi data, mengkompilasi templat, `mount` instan tersebut ke DOM, dan memperbarui DOM ketika data berubah. Di tengah-tengah proses tersebut, Vue juga menjalankan fungsi yang disebut **lifecycle hooks**, sehingga kalian bisa menambahkan kode kalian sendiri di tahap-tahap tertentu.

Sebagai contoh, [`created`](../api/#created) hook dapat digunakan untuk menjalankan kode setelah sebuah instan selesai dibuat:

```js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` mengarahkan ke instan vm
    console.log('a is: ' + this.a)
  }
})
// => "a adalah: 1"
```

Ada juga `hook` lainnya yang akan dipanggil pada berbagai tahap (lifecycle), seperti [`mounted`](../api/#mounted),  [`updated`](../api/#updated), dan [`destroyed`](../api/#destroyed). Semua lifecycle hook dipanggil dengan instan Vue yang memanggilnya sebagai nilai dari `this`.

<p class="tip">Jangan gunakan [Fungsi Panah (Arrow Function)](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) pada properti opsi atau panggilan balik (callback), seperti `created: () => console.log(this.a)` atau `vm.$watch('a', newValue => this.myMethod())`. Karena fungsi panah terikat pada konteks induk, `this` di dalam fungsi panah tidak akan merujuk ke instan Vue. Penggunaan fungsi panah ini sering menghasilkan kesalahan seperti `Uncaught TypeError: Cannot read property of undefined` atau `Uncaught TypeError: this.myMethod is not a function`.</p>

## Diagram Lifecycle

Di bawah ini adalah diagram untuk lifecycle sebuah instan.

_Diagram dalam Bahasa Inggris_

![Lifecycle Instan Vue](/images/lifecycle.png)
