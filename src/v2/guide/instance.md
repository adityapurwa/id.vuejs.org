---
title: Instan Vue
type: guide
order: 3
---

## Membuat Instan Vue

Setiap aplikasi Vue dimulai dengan membuat **instan Vue** baru dengan  fungsi `Vue`:

```js
var vm = new Vue({
  // pilihan
})
```

Meskipun tidak terkait erat dengan [pola MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), desain Vue sebagian terinspirasi oleh itu. Sebagai konvensi, kami sering menggunakan variabel `vm` (kependekan dari ViewModel) untuk merujuk ke instan Vue kami.

Saat kalian membuat instance Vue, kalian mengirimkan **objek opsi (options object)**. Mayoritas panduan ini menjelaskan bagaimana kalian dapat menggunakan opsi ini untuk membuat perilaku yang kalian inginkan. Sebagai bahan referensi, kalian juga dapat menelusuri daftar opsi lengkap dalam [referensi API](../api/#Options-Data).

Aplikasi Vue terdiri dari **root instan Vue (root Vue Instance)** yang dibuat dengan `new Vue`, secara opsional diatur ke dalam cabang komponen bersarang (nested), komponen yang dapat digunakan kembali. Misalnya, bagan komponen aplikasi todo yang mungkin akan terlihat seperti ini:

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

Nanti kami akan menjelaskan tentang [sistem komponen](components.html) lebih rinci. Untuk saat ini, perlu diketahui bahwa semua komponen Vue juga merupakan instan Vue, dan terima objek opsi yang sama (kecuali untuk beberapa opsi root-specific).

## Data dan Method

Ketika instan Vue dibuat, ia akan menambahkan semua properti yang ditemukan di `data` objeknya ke **sistem reaktivitas** Vue. Ketika nilai properti tersebut berubah, tampilan akan "bereaksi", memperbarui agar sesuai dengan nilai baru.

```js
// data obyek kalian
var data = { a: 1 }

// Sebuah obyek telah ditambahkan ke instan Vue
var vm = new Vue({
  data: data
})

// Mendapatkan properti pada instan
// Mengembalikan satu dari data asli
vm.a == data.a // => true

// Kelola properti pada instan
// juga memengaruhi pada data asli
vm.a = 2
data.a // => 2

// ... dan sebaliknya
data.a = 3
vm.a // => 3
```

Ketika data ini berubah, tampilan akan dirender ulang. Perlu dicatat bahwa properti di `data` hanya akan **reaktif** jika ada saat instance dibuat. Itu berarti jika kalian menambahkan properti baru, seperti:

```js
vm.b = 'hai'
```

Maka perubahan `b` tidak akan memicu pembaruan tampilan apa pun. Jika kalian tahu, bahwa nanti kalian akan membutuhkan properti, tetapi properti itu mulai kosong atau tidak ada, maka kalian harus menetapkan beberapa nilai awal. Sebagai contoh:

```js
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

Satu-satunya pengecualian untuk hal ini adalah penggunaan `Object.freeze()`, yang mencegah properti yang ada diubah, yang juga berarti sistem reaktivitas tidak dapat _melacak_ perubahan.

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

Selain properti data, instan Vue memaparkan sejumlah properti instan dan metode yang berguna. Ini diawali dengan `$` untuk membedakannya dari properti yang telah ditentukan pengguna. Sebagai contoh:

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
  // Ini pengembalian balik (callback) yang akan di panggil ketika `vm.a` berubah
})
```

Di masa mendatang, kalian dapat berkonsultasi dengan [referensi API](../api/#Instance-Properties) untuk daftar lengkap properti dan metode turunan.

## Instan Lifecycle Hooks

Setiap instan Vue melewati serangkaian langkah inisialisasi saat dibuat - misalnya, perlu mengatur observasi data, mengkompilasi template, `mount` instan ke DOM, dan memperbarui DOM ketika data berubah. Sepanjang jalan, itu juga menjalankan fungsi yang disebut **lifecycle hooks**, memberikan pengguna kesempatan untuk menambahkan kode mereka sendiri pada tahap tertentu.

Sebagai contoh, [`membuat`](../api/#created) hook dapat digunakan untuk menjalankan kode setelah sebuah instan dibuat:

```js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` mengarahkan ke vm instan
    console.log('a is: ' + this.a)
  }
})
// => "a adalah: 1"
```

Ada juga `hook` lainnya yang akan dipanggil pada berbagai tahap (lifecycle), seperti [`mounted`](../api/#mounted),  [`updated`](../api/#updated), dan [`destroyed`](../api/#destroyed). Semua lifecycle hook dipanggil dengan `this` yang konteksnya menunjuk ke instan Vue yang memintanya.

<p class="tip">Jangan gunakan [Fungsi Panah (Arrow Function)](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) pada properti opsi atau panggilan balik (callback), seperti `created: () => console.log(this.a)` atau `vm.$watch('a', newValue => this.myMethod())`. Karena fungsi panah terikat pada konteks induk, `this` tidak akan menjadi instan Vue seperti yang kalian harapkan, sering menghasilkan kesalahan seperti `Uncaught TypeError: Cannot read property of undefined` atau `Uncaught TypeError: this.myMethod is not a function`.</p>

## Diagram Lifecycle

Di bawah ini adalah diagram untuk instan Lifecycle. kalian tidak perlu sepenuhnya memahami segala sesuatu yang terjadi saat ini, tetapi saat kalian belajar dan membangun lebih banyak, itu akan menjadi referensi yang sangat berguna.

![Lifecycle Instan Vue](/images/lifecycle.png)
