---
title: Kelas dan Style Bindings 
type: guide
order: 6
---

Kebutuhan umum dari suatu pengikatan data adalah memanipulasi daftar kelas elemen dan kumpulan _inline styles_ yang ada. Karena keduanya merupakan sama-sama berbentuk atribut, kita dapat menggunakan `v-bind` untuk mengatasinya: kita hanya perlu melakukan kalkulasi sebuah _string_ akhir dari ekspresi-ekspresi kita. Akan tetapi, mencampur adukkan dengan penggabungan _string_ itu merupakan suatu hal yang cukup mengganggu dan rawan terjadi kesalahan. Dengan alasan ini, Vue menyediakan perbaikan khusus ketika `v-bind` digunakan dengan `class` dan `style`. Sebagai tambahan pada kumpulan _string_, ekspresi juga dapat menilai obyek atau _array_.

## Mengikat Kelas HTML

### Sintaks Obyek

Kita dapat memberikan sebuah obyek ke `v-bind:class` untuk mengalihkan kelas secara dinamis:

``` html
<div v-bind:class="{ active: isActive }"></div>
```

Sintaks di atas berarti dengan adanya kelas `active` akan ditentukan oleh [kebenaran](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) dari data properti `isActive`.

Kalian dapat memiliki beberapa kelas yang dialihkan dengan memiliki beberapa tambahan _field_ di dalam obyek. Sebagai tambahan, direktif `v-bind:class` juga dapat berdampingan dengan atribut `class` biasa. Jadi, mari kita lihat beberapa templat berikut:

``` html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

Dan data berikut ini:

``` js
data: {
  isActive: true,
  hasError: false
}
```

Itu akan me-_render_:

``` html
<div class="static active"></div>
```

Ketika `isActive` atau `hasError` berubah, maka dari itu daftar kelas akan diperbarui. Contohnya, jika `hasError` menjadi `true`, daftar kelas akan berubah menjadi `"static active text-danger"`.

Obyek yang diikat tidak harus _inline_:

``` html
<div v-bind:class="classObject"></div>
```

``` js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

Ini akan menghasilkan hasil _render_ yang sama. Kita juga dapat mengikat ke [properti terkomputasi](computed.html) yang mengembalikan sebuah obyek. Ini merupakan pola yang umum dan memiliki banyak kelebihan:

``` html
<div v-bind:class="classObject"></div>
```

``` js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### Sintaks Array

Kita dapat memberikan sebuah _array_ kepada `v-bind:class` untuk menerapkan sebuah daftar dari beberapa kelas:

``` html
<div v-bind:class="[activeClass, errorClass]"></div>
```

``` js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

Yang akan mengasilkan _render_:

``` html
<div class="active text-danger"></div>
```

Jika kalian juga ingin mengalihkan sebuah kelas pada sebuah daftar secara kondisional, kalian juga dapat melakukannya menggunakan ekspresi _ternary_:

``` html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

Hal ini juga akan selalu menerapkan `errorClass`, tapi hanya akan menerapkan `activeClass` ketika `isActive` bernilai benar.

Bagaimanapun juga, ini akan sedikit berlebihan jika kalian menggunakan banyak kelas yang kondisional. Itulah mengapa memungkinkan juga bagi kita untuk menggunakan sintaks obyek di dalam sintaks _array_:

``` html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### Dengan Komponen-Komponen

> Di bagian ini diasumsikan kita telah memiliki pengetahuan dari [Komponen-komponen Vue](components.html). Kalian dapat melewatkan bagian ini dan kembali lagi nanti.

Ketika kalian menggunakan atribut `class` pada sebuah komponen kustom, kelas tersebut akan ditambahkan pada elemen komponen sumber. Kelas yang ada pada elemen ini tidak akan digantikan.

Sebagai contoh, jika kalian mendeklarasikan komponen ini:

``` js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

Lalu menambahkan beberapa kelas ketika menggunakan komponen itu:

``` html
<my-component class="baz boo"></my-component>
```

HTML yang di-_render_ akan menghasilkan:

``` html
<p class="foo bar baz boo">Hi</p>
```

Hal yang sama juga bernilai benar untuk pengikatan kelas:

``` html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

Ketika `isActive` bernilai benar, akan menghasilkan HTML seperti berikut:

``` html
<p class="foo bar active">Hi</p>
```

## Pengikatan Inline Styles

### Sintaks Obyek

Sintaks obyek dari `v-bind:style` cukup jelas - terlihat seperti kebanyakan CSS, kecuali itu adalah obyek JavaScript. Kalian dapat menggunakanya dalam bentuk _camelCase_ atau _kebab-case_ (gunakan petik ganda untuk _kebab-case_) untuk nama properti CSS:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

Merupakan ide yang bagus untuk mengikat obyek _style_ secara langsung sehingga templat terlihat lebih bersih dan jelas:

``` html
<div v-bind:style="styleObject"></div>
```

``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

Sekali lagi, sintaks obyek ini sering digunakan di dalam hubungan dengan properti terkomputasi yang mengembalikan obyek.

### Sintaks Array

Sintaks _array_ untuk `v-bind:style` memungkinkan kita untuk menerapkan banyak obyek _style_ ke elemen yang sama:

``` html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### Awalan Otomatis

Ketika kalian menggunakan properti CSS yang membutuhkan [vendor prefixes](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) dalam `v-bind:style`, contohnya `transform`, Vue akan secara otomatis mendeteksi dan menambahkan awalan yang sesuai untuk menerapkan _style_.

### Dukungan Banyak Nilai

> 2.3.0+

Sejak versi 2.3.0+ kalian dapat menyediakan sebuah _array_ dari banyak (_prefixed_) nilai kepada sebuah properti _style_, sebagai contoh:

``` html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Ini hanya akan menghasilkan nilai akhir pada _array_ yang didukung oleh peramban. Pada contoh ini, ini akan menghasilkan `display: flex` untuk peramban yang mendukung versi _unprefixed flexbox_.
