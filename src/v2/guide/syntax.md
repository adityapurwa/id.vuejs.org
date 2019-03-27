---
title:  Sintaks Templat
type: guide
order: 4
---

Vue.js menggunakan sintaks templat berbasis HTML yang memungkinkan kalian untuk mengikat hasil render DOM secara deklaratif kepada pokok data instan Vue. Semua templat Vue.js merupakan HTML valid yang dapat diurai oleh peramban yang memenuhi spesifikasi dan pengurai HTML.

Cara kerjanya, Vue melakukan kompilasi templat menjadi fungsi-fungsi render Virtual DOM. Dikombinasikan dengan sistem yang reaktif, Vue dapat secara cerdas mencari tahu jumlah minimal komponen-komponen yang di-render ulang dan menerapkan jumlah minimal dari manipulasi DOM ketika state aplikasi berubah.

Jika kalian terbiasa dengan konsep _Virtual DOM_ dan lebih memilih menggunakan Javascript, kalian juga bisa [langsung membuat fungsi-fungsi render](render-function.html) daripada menggunakan templat dengan dukungan opsional JSX.

## Penyisipan

### Teks

Bentuk paling mendasar dari pengikatan data merupakan penyisipan text menggunakan syntax "Mustache" (kurung kurawal ganda):

``` html
<span>Message: {{ msg }}</span>
```

Tag _mustache_ akan digantikan oleh nilai dari properti `msg` pada obyek data yang bersesuaian. Data tersebut juga akan diperbarui ketika properti obyek data `msg` terdapat perubahan.

Kalian juga dapat melakukan penyisipan sekali yang tidak memperbarui perubahan data dengan menggunakan [direktif v-once](../api/#v-once), tapi perlu diingat hal ini juga akan mempengaruhi proses pengikatan data di _node_ yang sama.

``` html
<span v-once>This will never change: {{ msg }}</span>
```

### HTML Murni

Kurung kurawal ganda mengartikan data sebagai teks biasa, bukan HTML. Agar menampilkan keluaran HTML yang sesungguhnya, kalian akan membutuhkan pemakaian direktif `v-html`:

``` html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

{% raw %}
<div id="example1" class="demo">
  <p>Using mustaches: {{ rawHtml }}</p>
  <p>Using v-html directive: <span v-html="rawHtml"></span></p>
</div>
<script>
new Vue({
  el: '#example1',
  data: function () {
    return {
      rawHtml: '<span style="color: red">This should be red.</span>'
    }
  }
})
</script>
{% endraw %}

Isi `span` akan digantikan oleh nilai dari properti `rawHtml`, mengartikan sebagai HTML biasa - pengikatan data akan diabaikan. Perlu diingat kalian tidak dapat menggunakan `v-html` untuk membuat templat yang terpisah, karena Vue bukan mesin templat berbasis _string_. Melainkan komponen-komponen yang lebih sesuai sebagai unit dasar dari pemakaian ulang UI dan komposisi.

<p class="tip">Melakukan render HTML dinamis secara sembarangan pada situs web bisa sangat berbahaya karena dapat dengan mudah menyebabkan [kerentanan XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Gunakan penyisipan HTML hanya pada isian yang terpercaya dan **jangan pernah** pada isian yang disediakan oleh pengguna.</p>

### Atribut-Atribut

Kurung kurawal tidak dapat digunakan di dalam atribut HTML. Sebagai gantinya, gunakan [direktif v-bind](../api/#v-bind)

``` html
<div v-bind:id="dynamicId"></div>
```

Dalam kasus atribut _boolean_, di mana atribut tersebut tidak lebih dari menunjukkan adanya nilai `true`, `v-bind` berfungsi sedikit berbeda. Pada contoh ini:

``` html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

Jika `isButtonDisabled` memiliki nilai `null`, `undefined`, or `false`, atribut `disabled` tidak akan disertakan pada elemen `button`.

### Menggunakan Ekspresi JavaScript

Sejauh ini kita hanya melakukan pengikatan kunci properti sederhana pada templat kita. Tapi Vue.js sesungguhnya mendukung ekspresi JavaScript sepenuhnya di dalam semua pengikatan data:

``` html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

Ekspresi ini akan diperiksa sebagai JavaScript pada cakupan data dari pemilik instan Vue. Ada satu pembatasan yaitu setiap aksi pengikatan hanya bisa berisi **satu ekspresi**, sehingga ekspresi setelahnya **tidak** akan berjalan:

``` html
<!-- Ini adalah pernyataan, bukan ekspresi: -->
{{ var a = 1 }}

<!-- kendali alur tidak akan berjalan, gunakan ekspresi ternary -->
{{ if (ok) { return message } }}
```

<p class="tip">Ekspresi templat itu terisolasi dan hanya memiliki akses kepada daftar global yang diperbolehkan seperti `Math` dan `Date`. Kalian tidak harus mencoba mengakses global yang ditetapkan pengguna pada ekspresi templat.</p>

## Direktif

Direktif merupakan atribut spesial dengan prefix `v-`. Nilai atribut direktif diharapkan berupa **sebuah ekspresi JavaScript** (dengan pengecualian dari `v-for`, yang akan kita bahas nanti). Fungsi direktif adalah untuk menerapkan efek samping secara reaktif kepada DOM ketika nilai dari suatu ekspresi berubah. Mari kita ulas contoh yang kita lihat pada pendahuluan:

``` html
<p v-if="seen">Now you see me</p>
```

Di sini, direktif `v-if` akan menghilangkan atau memasukkan elemen `<p>` berdasarkan kebenaran nilai dari ekspresi `seen`.

### Argumen

Beberapa direktif dapat menerima "argumen", dinotasikan menggunakan titik dua setelah nama direktif. Sebagai contoh, direktif `v-bind` digunakan secara reaktif untuk memperbarui atribut HTML:

``` html
<a v-bind:href="url"> ... </a>
```

Di sini `href` merupakan sebuah argumen, yang memberitahukan direktif `v-bind` untuk mengikat elemen dari atribut `href` kepada nilai ekspresi `url`.

Contoh lainnya ada pada direktif `v-on`, yang mana mendengarkan _event_ pada sebuah DOM.

``` html
<a v-on:click="doSomething"> ... </a>
```

Argumen di sini merupakan sebuah nama _event_ yang didengarkan. Kita juga akan membahas lebih detail tentang penanganan _event_

### Argumen Dinamis

> Baru di 2.6.0+

Mulai dari versi 2.6.0, memungkinkan untuk menggunakan ekspresi JavaScript dalam sebuah argumen direktif dengan membungkusnya menggunakan kurung siku:

``` html
<a v-bind:[attributeName]="url"> ... </a>
```

Di sini `attributeName` akan secara dinamis diperiksa sebagai ekspresi JavaScript, dan nilai yang diperiksa akan digunakan sebagai nilai akhir dari argumen. Contoh, jika instan Vue kalian memiliki properti data, `attributeName`, yang memiliki nilai `"href"`, selanjutnya pengikatan atribut ini akan bernilai sama dengan `v-bind:href`.

Demikian juga kalian dapat menggunakan argumen dinamis untuk mengikat sebuah _handler_ ke dalam nama _event_ dinamis:

``` html
<a v-on:[eventName]="doSomething"> ... </a>
```

Sama halnya ketika nilai `eventName` adalah `"focus"`, sebagai contoh, `v-on:[eventName]` akan bernilai sama dengan `v-on:focus`.

#### Pembatasan Nilai Argumen Dinamis

Argumen-argumen dinamis diharapkan untuk memeriksa _string_ dengan pengecualian dari `null`. Nilai spesial `null` dapat digunakan secara eksplisit untuk menghapus ikatan. Semua nilai yang bukan _string_ akan memicu munculnya peringatan.

#### Pembatasan Ekspresi Argumen Dinamis

<p class="tip">Ekspresi argumen dinamis memiliki beberapa batasan sintaks karena karakter-karakter tertentu tidak dapat digunakan di dalam nama atribut HTML, seperti spasi atau petik tunggal. Kalian juga perlu menghindari huruf besar ketika menggunakan templat di dalam DOM</p>

Contoh berikut merupakan bentuk yang tidak valid:

``` html
<a v-bind:['foo' + bar]="value"> ... </a>
```

Solusinya adalah menggunakan ekspresi tanpa menggunakan spasi atau petik tunggal, atau menggantinya dengan ekspresi yang kompleks menggunakan properti _computed_.

Sebagai tambahan, jika kalian menggunakan templat dalam DOM (templat secara langsung ditulis di dalam sebuah berkas HTML), kalian harus tahu bahwa peramban akan memaksa nama atribut menjadi huruf kecil. 

``` html
<!-- Pada templat di dalam DOM, ini akan diganti menjadi v-bind:[someattr] -->
<a v-bind:[someAttr]="value"> ... </a>
```

### Modifiers

_Modifiers_ merupakan kumpulan _postfixes_ khusus yang dinotasikan menggunakan titik, mengindikasikan bahwa sebuah direktif haruslah terikat dengan cara yang khusus. Contohnya, pengubah `.prevent` memberitahukan direktif `v-on` untuk memanggil `event.preventDefault()` pada kejadian yang dipicu:

``` html
<form v-on:submit.prevent="onSubmit"> ... </form>
```

Kita akan melihat contoh-contoh lain dari pengubah nantinya, [untuk `v-on`](events.html#Event-Modifiers) and [untuk `v-model`](forms.html#Modifiers), ketika kita menjelajahi fitur-fitur tersebut.

## Bentuk Sederhana

Awalan `v-` melayani sebagai isyarat visual untuk mengidentifikasi atribut Vue yang spesifik di dalam templat-templat kalian. Ini berguna ketika kalian menggunakan Vue.js untuk menerapkan perilaku dinamis kepada _markup_ yang ada, tapi terasa berlebihan untuk beberapa direktif yang sering digunakan. Di saat yang sama, kebutuhan awalan `v-` menjadi kurang penting ketika kalian sedang membangun sebuah [SPA](https://en.wikipedia.org/wiki/Single-page_application), di mana Vue mengatur setiap templat. Karena itu, Vue menyediakan bentuk sederhana untuk dua direktif yang paling sering digunakan, `v-bind` dan `v-on`:

### Bentuk Sederhana `v-bind`

``` html
<!-- sintaks lengkap -->
<a v-bind:href="url"> ... </a>

<!-- penyederhanaan -->
<a :href="url"> ... </a>

<!-- Penyederhanaan dengan argumen dinamis (2.6.0+) -->
<a :[key]="url"> ... </a>
```

### Bentuk Sederhana `v-on`

``` html
<!-- Sintaks lengkap -->
<a v-on:click="doSomething"> ... </a>

<!-- Penyederhanaan -->
<a @click="doSomething"> ... </a>

<!-- Penyederhanaan dengan argumen dinamis (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

Beberapa bentuk sintaks di atas mungkin terlihat sedikit berbeda dari HTML yang normal, tapi `:` dan `@` adalah karakter yang sah untuk nama atribut dan seluruh peramban pendukung Vue dapat menguraikannya dengan benar. Sebagai tambahan, mereka tidak akan muncul sebagai markup akhir hasil render. Sintaks yang disederhanakan adalah opsional secara keseluruhan, namun kalian kemungkinan akan menyukainya ketika kalian mempelajari kegunaannya lebih banyak nanti.
