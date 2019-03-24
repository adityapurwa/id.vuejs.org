---
title: Custom Direktif
type: guide
order: 302
---

## Pendahuluan

Sebagai tambahan kepada kumpulan direktif standar yang terdapat dalam inti (`v-model` and `v-show`), Vue juga mengizinkan kalian untuk mendaftarkan direktif-direktif custom kalian sendiri. Perlu diperhatikan bahwa pada Vue 2.0, bentuk dasar dari penggunaan ulang kode dan abstraksi adalah komponen-komponen - namun ada banyak kasus dimana kalian membutuhkan beberapa akses _low-level DOM_ pada elemen-elemen sederhana, seperti ini:

{% raw %}
<div id="simplest-directive-example" class="demo">
  <input v-focus>
</div>
<script>
Vue.directive('focus', {
  inserted: function (el) {
    el.focus()
  }
})
new Vue({
  el: '#simplest-directive-example'
})
</script>
{% endraw %}

Ketika memuat halaman, elemen tersebut mendapatkan fokus (catatan: `autocofus` tidak berjalan pada Safari versi _mobile_). Kenyataannya, jika kalian belum melakukan klik pada bagian manapun sejak mengunjungi halaman ini, isian di atas seharusnya difokuskan untuk saat ini. Sekarang mari kita membuat direktif yang dapat menyelesaikan hal ini:

``` js
// Mendaftarkan sebuah custom direktif global yang bernama `v-focus`
Vue.directive('focus', {
  // Ketika ikatan elemen dimasukkan ke dalam DOM...
  inserted: function (el) {
    // Fokuskan elemen
    el.focus()
  }
})
```

Jika kalian ingin mendaftarkan sebuah direktif secara lokal sebagai gantinya, komponen-komponen juga menerima sebuah pilihan `directives`:

``` js
directives: {
  focus: {
    // pendefinisian direktif
    inserted: function (el) {
      el.focus()
    }
  }
}
```

Selanjutnya dalam sebuah _template_, kalian dapat menggunakan atribut baru `v-focus` pada elemen manapun, seperti berikut:

``` html
<input v-focus>
```

## Fungsi Hook

Sebuah definisi obyek direktif dapat menyediakan beberapa fungsi-fungsi _hook_ (semuanya opsional):

- `bind`: hanya dipanggil sekali, ketika direktif terikat pertama kali pada sebuah elemen. Hal ini dimana kalian dapat melakukan pekerjaan pengaturan yang dilakukan sekali saja.

- `inserted`: dipanggil ketika ikatan elemen telah dimasukkan ke dalam _parent node_ (ini hanya menjamin keberadaan _parent node_, tidak harus dalam-dokumen).

- `update`: dipanggil setelah isi komponen VNode telah diperbarui, __tapi memungkinkan sebelum child diperbarui__. Nilai direktif mungkin berubah atau mungkin tidak berubah, tapi kalian bisa melewati pembaruan yang tidak perlu dengan membandingkan _binding_ saat ini dan nilai lama (Lihat di bawah ini pada argumen _hook_).

<p class="tip">Kita akan membahas VNodes lebih rinci [nanti](./render-function.html#The-Virtual-DOM), ketika kita mendiskusikan [fungsi render](./render-function.html).</p>

- `componentUpdated`: dipanggil setelah isi komponent VNode __dan VNode dari child__ yang telah diperbarui.

- `unbind`: hanya dipanggil sekali, ketika direktif terlepas dari elemen.

Kita akan menjelajahi argumen-argumen yang dapat dimasukkan kepada _hook_ tersebut (contoh. `el`, `binding`, `vnode`, dan `oldVnode`) pada bagian selanjutnya.

## Argumen Direktif Hook

Direktif _hook_ menggunakan argumen-argumen sebagai berikut:

- `el`: Elemen yang mengikat suatu direktif. Elemen ini yang akan digunakan secara langsung untuk memanipulasi DOM.
- `binding`: Sebuah obyek yang berisi properti-properti sebagai berikut:
  - `name`: Nama direktif, tanpa prefix `v-`.
  - `value`: Nilai yang dilewatkan pada direktif. Contoh dalam `v-my-directive="1 + 1"`, nilainya adalah `2`.
  - `oldValue`: Nilai sebelumnya, hanya tersedia pada `update` dan `componentUpdated`. Argumen ini tersedia apakah nilai sudah berubah atau tidak.
  - `expression`: Ekspresi dari sebuah _binding_ sebagai _string_. Contoh pada `v-my-directive="1 + 1"`, ekspresi yang dimaksud adalah `"1 + 1"`.
  - `arg`: Argumen yang dilewatkan kepada direktif, jika ada. Contoh pada `v-my-directive:foo`, `arg` yang dimaksud adalah `"foo"`.
  - `modifiers`: Sebuah obyek yang berisi kumpulan _modifier_, jika ada. Contoh pada `v-my-directive.foo.bar`, kumpulan obyek _modifier_ yang dimaksud adalah `{ foo: true, bar: true }`.
- `vnode`: Merupakan _virtual node_ yang dihasilkan oleh kompiler Vue. Lihat [API VNode](../api/#VNode-Interface) untuk penjelasan lebih rinci.
- `oldVnode`: Merupakan _virtual node_ yang dihasilkan sebelumnya, hanya tersedia pada _hook_ `update` dan `componentUpdated`.

<p class="tip">Selain `el`, kalian seharusnya memperlakukan argumen-argumen tersebut sebagai _read-only_ dan tidak pernah melakukan perubahan terhadapnya. Jika kalian ingin berbagi informasi terhadap _hook_-_hook_ yang ada, disarankan untuk melakukannya melalui elemen [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset).</p>

Contoh dari direktif custom yang menggunakan beberapa properti sebagai berikut:

``` html
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
```

``` js
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
```

{% raw %}
<div id="hook-arguments-example" v-demo:foo.a.b="message" class="demo"></div>
<script>
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})
new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})
</script>
{% endraw %}

## Fungsi Singkatan

Di beberapa kasus, kalian mungkin ingin perilaku yang sama pada `bind` dan `update`, akan tetapi tidak peduli terhadap _hook_ lainnya. Sebagai contoh berikut:

``` js
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

## Obyek Literal

Jika direktif kalian membutuhkan lebih dari satu nilai, kalian dapat juga melewatkan sebuah obyek literal JavaScript. Perlu diingat, direktif-direktif dapat mengambil nilai valid apapun dari ekspresi JavaScript.

``` html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

``` js
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```
