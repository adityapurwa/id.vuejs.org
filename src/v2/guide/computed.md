---
title: Properti Terkomputasi dan Pengamat (_Watcher_)
type: guide
order: 5
---

## Properti Terkomputasi

Ekspresi dalam templat memang sangat memudahkan pengembangan, tapi mereka hanya disarankan untuk operasi sederhana. Ekspresi yang rumit di dalam templat bisa membuat templat membengkak dan susah untuk dirawat. Contohnya:

``` html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

Di kasus ini, templat yang ada terlihat rumit dan tidak deklaratif. Butuh waktu beberapa detik untuk menyadari bahwa contoh di atas akan menampilkan `message` secara terbalik. Masalah ini juga akan bertambah jika kalian ingin menampilkan `message` terbalik ini di banyak tempat.

Karena itu, untuk ekspresi yang sudah mulai rumit, kalian bisa menggunakan **computed property**.

### Contoh Sederhana

``` html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // getter yang terkomputasi
    reversedMessage: function () {
      // `this` merujuk ke instan vue
      return this.message.split('').reverse().join('')
    }
  }
})
```

Hasilnya:

{% raw %}
<div id="example" class="demo">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Di contoh atas, kita membuat properti terkomputasi dengan nama `reversedMessage`. Fungsi yang kita sediakan akan dipanggil sebagai getter untuk properti `vm.reversedMessage`:

``` js
console.log(vm.reversedMessage) // => 'olleH'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'eybdooG'
```

Kalian bisa membuka konsol dan mencoba contoh di atas. Nilai dari `vm.reversedMessage` akan selalu bergantung kepada nilai dari `vm.message`.

Kalian bisa melakukan _data-bind_ ke properti terkomputasi di templat seperti halnya properti biasa. Vue tahu bahwa `vm.reversedMessage` bergantung kepada `vm.message` , jadi Vue akan memperbarui _binding_ yang bergantung pada `vm.reversedMessage` ketika `vm.message` berubah. Kita secara deklaratif membuat relasi ketergantungan ini ketika kita membuat properti terkomputasi, dan fungsi properti terkomputasi ini tidak memiliki efek samping, sehingga lebih mudah untuk diuji dan dimengerti.

### _Caching_ Komputasi vs _Method_

Jika kalian amati, kalian bisa mendapatkan hasil yang sama dengan memanggil properti terkomputasi sama dengan layaknya memanggil sebuah _method_.

``` html
<p>Reversed message: "{{ reverseMessage() }}"</p>
```

``` js
// di komponen
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

Daripada menjadikannya properti terkomputasi kita bisa membuatnya menjadi _method_. Hasilnya akan mirip, bedanya adalah **properti terkomputasi akan di-_cache_ berdasarkan ketergantungan reaktifnya**. Properti terkomputasi hanya akan dievaluasi ulang ketika ketergantungan reaktifnya berubah. Pada kasus di atas, jika `message` sebagai ketergantungan reaktif dari `reversedMessage` tidak berubah, akses berulang-ulang ke `reversedMessage` akan langsung mengembalikan nilai yang sebelumnya sudah dikomputasi  tanpa harus menjalankan fungsi komputasinya lagi.

Jika kalian amati contoh di bawah, properti terkomputasi di bawah tidak akan pernah diperbarui, karena `Date.now()` bukanlah ketergantungan reaktif.

``` js
computed: {
  now: function () {
    return Date.now()
  }
}
```

Sebaliknya ketika menggunakannya sebagai method, fungsi di atas akan dipanggil setiap terjadi proses _re-render_.

Kenapa butuh dilakukan proses _caching_? Bayangkan jika fungsi properti terkomputasi **A** melakukan proses perulangan dari sebuah _array_ yang besar dan ada banyak proses di dalam perulangan tersebut. Selain itu, proses evaluasi ulang juga akan dilakukan untuk properti terkomputasi lain yang bergantung pada properti terkopmutasi **A** tersebut. Tanpa proses _caching_, Vue akan menjalankan fungsi tersebut berkali-kali! Jika kalian benar-benar tidak ingin ada proses _caching_, gunakan _method_ saja.

### Properti Terkomputasi vs Properti Teramati (_Watched_)

Vue juga menyediakan cara yang lebih umum untuk mengamati perubahan data di instan Vue melalui **properti watch**. Ketika kalian butuh merubah data berdasarkan data lain yang berubah, mungkin terlintas pemikiran untuk menggunakan `watch` - terutama jika kalian terbiasa dengan AngularJS. Tetapi, pada umumnya lebih baik menggunakan properti terkomputasi daripada `watch`. Contohnya:


``` html
<div id="demo">{{ fullName }}</div>
```

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```
Code di atas sangat imperatif dan repetitif. Coba bandingkan dengan contoh yang menggunakan properti terkomputasi:

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

Lebih baik kan?

### _Setter_ Terkomputasi

Properti terkomputasi pada dasarnya hanya akan menjadi _getter_, tapi kalian juga bisa membuat _setter_-nya:

``` js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Jika kalian menjalankan `vm.fullName = 'John Doe'`, fungsi _setter_ di atas akan dipanggil dan nilai dari `vm.firstName` dan `vm.lastName` juga akan berubah.

## Pengamat / _Watcher_

Meskipun pada umumnya properti terkomputasi cocok untuk hampir semua kasus, ada kalanya kita butuh pengamat / _watcher_. Karena itu Vue memberikan cara untuk mengamati perubahan data ini melalui opsi `watch`. Umumnya berguna ketika kalian butuh menjalankan proses asinkronus atau operasi berat ketika ada data yang berubah.

Contoh:

``` html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

``` html
<!-- Karena sudah ada banyak library yang berhubungan dengan ajax atau fungsionalitas umum lainnya, -->
<!-- Vue tidak menyediakan fungsionalitas tersebut supaya Vue tidak -->
<!-- mencoba _reinventing-the-wells_ (proses pembuatan sesuatu yang sudah dibuat sebelumnya), -->
<!-- dan memberikan kalian kebebasan untuk memilih yang kalian suka -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // Setiap data.question berubah, fungsi di bawah akan dijalankan
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // _.debounce adalah fungsi yang disediakan lodash untuk membatasi
    // seberapa sering operasi suatu fungsi dijalankan.
    // Dalam kasus ini, kita ingin membatasi seberapa sering kita memanggil
    // yesno.wtf/api, dengan menunggu sampai pengguna
    // selesai mengetik sebelum melakukan permintaan ajax. Untuk belajar lebih lanjut
    // tentang fungsi _.debounce (dan yang mirip dengannya
    // _.throttle), kunjungi: https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer:  function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```

Hasil:

{% raw %}
<div id="watch-example" class="demo">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer:  function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
{% endraw %}

Dalam kasus ini, penggunaan opsi `watch` memberikan kita cara untuk melakukan proses asinkronus (memanggil sebuah _API_), membatasi seberapa sering proses tersebut dilakukan, dan mengatur _state_ sementara sampai kita mendapatkan hasil dari proses asinkronus tersebut. Proses seperti ini tidak bisa dilakukan melalui properti terkomputasi.

Selain opsi `watch`, kalian juga bisa menggunakan [API vm.$watch](../api/#vm-watch).
