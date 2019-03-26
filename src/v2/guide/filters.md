---
title: Filter
type: guide
order: 305
---

Kalian bisa mendefinisikan sebuah _filter_ untuk melakukan proses _formatting_ sebuah teks di Vue.js. Filter bisa digunakan di dua tempat: di antara dua kurawal, atau di dalam ekspresi `v-bind` (didukung di versi 2.1.0 ke atas). Filter dipanggil dengan cara menambahkan tanda "pipa" di akhir ekspresi JavaScript.

``` html
<!-- antar dua kurawal / mustache -->
{{ message | capitalize }}

<!-- di v-bind -->
<div v-bind:id="rawId | formatId"></div>
```

Kalian bisa mendefinisikan filter lokal melalui opsi sebuah komponen:

``` js
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```

atau definisikan filter global sebelum membuat instan Vue:

``` js
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  // ...
})
```

Di bawah adalah contoh penggunaan filter `capitalize`:

{% raw %}
<div id="example_1" class="demo">
  <input type="text" v-model="message">
  <p>{{ message | capitalize }}</p>
</div>
<script>
  new Vue({
    el: '#example_1',
    data: function () {
      return {
        message: 'john'
      }
    },
    filters: {
      capitalize: function (value) {
        if (!value) return ''
        value = value.toString()
        return value.charAt(0).toUpperCase() + value.slice(1)
      }
    }
  })
</script>
{% endraw %}

Setiap fungsi filter mempunyai parameter pertama yang berisi nilai dari ekspresi sebelum filter tersebut. Di contoh atas, fungsi `capitalize` akan menerima nilai dari variabel `message` sebagai parameter pertamanya.

Filter bisa digabungkan:

``` html
{{ message | filterA | filterB }}
```

Dalam kasus ini, `filterA`, akan menerima nilai dari `message`, lalu `filterB` akan menerima hasil dari `filterA`.

Filter adalah fungsi JavaScript biasa, sehingga kita bisa memanggilnya dengan parameter lain juga:

``` html
{{ message | filterA('arg1', arg2) }}
```

Di sini `filterA` didefinisikan sebagai fungsi yang menerima tiga parameter. Nilai variabel `message` akan masuk sebagai parameter pertama. Sedangkan teks `'arg1'` akan masuk sebagai parameter kedua, dan nilai dari `arg2` akan masuk sebagai parameter ketiga.