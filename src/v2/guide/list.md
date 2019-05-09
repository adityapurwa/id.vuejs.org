---
title: Me-render Daftar
type: guide
order: 8
---

## Memetakan sebuah Array ke Elemen dengan `v-for`

Kita dapat menggunakan direksi `v-for` untuk me-render sebuah daftar _item_ berdasakan dari sebuah array. Direksi `v-for` membutuhkan sebuah sintaks spesial dalam bentuk `item in items`, dimana `items` adalah sumber data array dan `item` adalah **alias** untuk elemen array yang diperulangkan:

``` html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

Result:

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items">
    {{item.message}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(document.querySelector('#example-1'))
    }
  }
})
</script>
{% endraw %}

Didalam blok `v-for` kita memiliki akses penuh kepada cangkupan properti _parent_. `v-for` juga mendukung sebuah argumen opsional kedua untuk index dari item saat ini.

``` html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

Result:

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(document.querySelector('#example-2'))
    }
  }
})
</script>
{% endraw %}

Kalian juga bisa menggunakan `of` sebagai pembatass sebagai pengganti `in`, supaya lebih mirip dengan sintaks dari JavaScript untuk perulangan:

``` html
<div v-for="item of items"></div>
```

## `v-for` dengan sebuah Objek

Kalian juga bisa menggunakan `v-for` untuk melakukan perulangan melalui properti dari sebuah objek.

``` html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

``` js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
```

Result:

{% raw %}
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
</script>
{% endraw %}

Kalian juga dapat menyediakan sebuah argumen ke dua sebagai kunci:

``` html
<div v-for="(value, key) in object">
  {{ key }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-key" class="demo">
  <div v-for="(value, key) in object">
    {{ key }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-key',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
</script>
{% endraw %}

Dan argumen lain sebagai index:

``` html
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-key-index" class="demo">
  <div v-for="(value, key, index) in object">
    {{ index }}. {{ key }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-key-index',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
</script>
{% endraw %}

<p class="tip">Ketika melakukan perulangan pada sebuah objek, urutannya berdasarkan pada urutan penomoran kunci dari `Object.keys()`, yang mana **tidak** digaransi untuk dapat konsisten pada sebagian besar implementasi mesin JavaScript.</p>

## `key`

Ketika Vue memperbarui sebuah daftar elemen yang di-render dengan `v-for`, secara standar menggunakan sebuah strategi "_patch_ di-tempat". Jika urutan datanya berubah, dibanding memindahkan elemen DOM untuk mencocokkan urutan dari item tersebut, Vue akan melakukan patch ke setiap element di-tempat dan memastikan untuk menampilkan apa yang seharusnya di-render pada index tertentu tersebut. Hal ini mirip dengan kelakuan dari `track-by="$index"` pada Vue 1.x.

Mode standar ini efisian, tetapi hanya cocok **ketika render output daftar kalian tidak bergantung pada komponen _state_ _child_ atau state DOM sementara (contohnya nilai input formulir)**.

Untuk memberikan Vue sebuah petunjuk agar dapat melacak setiap identitas node, yang kemudian menggunakan ulang dan mengurutkan kembali element yang sudah ada, kalian harus menyediakan sebuah attribut `key` yang unik untuk setiap item. Sebuah nilai ideal untuk `key` akan menjadi id unik untuk setiap item. Attribut spesial ini kurang lebih sama dengan `track-by` pada 1.x, tetapi bekerja seperti attribut, jadi kalian harus menggunakan `v-bind` untuk mengikatnya agar nilainya dinamis (disini menggunakan shorthand):

``` html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

Direkomendasikan untuk menyediakan sebuah `key` bersama dengan `v-for` kapanpun, kecuali konten DOM yang diperulangkan sederhana, atau kalian sengaja mengandalkan perbuatan standar untuk meningkatkan performa.

Karena mengidentifikasi node adalah mekanisme umum untuk Vue, `key` tersebut juga memiliki kegunaan lain yang tidak dibuat khusus untuk `v-for`, yang akan kita lihat di panduan nanti.

<p class="tip">Jangan menggunakan nilai _non-primitive_ seperti objek dan array untuk kunci `v-for`. Gunakan _string_ atau nilai angka saja.</p>

## Pendeteksian Perubahan Array

### Metode Mutasi

Vue membukus metode mutasi dari array yang diamati agar mereka akan dapat memicu pembaruan tampilan. Metode yang dibungkus adalah:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Kalian bisa membuka _console_ dan bermain dengan contoh array `items` sebelumnya dengan memanggil metode mutasi mereka. Contohnya: `example1.items.push({ message: 'Baz' })`.

### Menggantikan sebuah Array

Metode mutasi, seperti namanya, memutasi array asli dimana mereka dipanggil. Sebagai perbandingan, ada juga metode non-mutasi, contohnya `filter()`, `concat()` dan `slice()`, dimana tidak memutasi array asli tetapi **selalu mengembalikan sebuah array baru**. Ketika bekerja dengan metode non-mutasi, kalian dapat mengganti array lama dengan array yang baru:

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

Kalian mungkin pikir hal ini akan menyebabkan Vue untuk membuang DOM yang sudah ada dan melakukan render lagi ke semua daftar - beruntungnya, bukan itu yang terjadi. Vue mengimplementasi beberapa herustis pintar untuk memaksimalkan penggunaan kembali elemen DOM, jadi operasi mengganti sebuah array dengan array lain yang berisikan objek tumpang tindih sangat efisien.

### Kekurangan

Karena batasan-batasan pada JavaScript, Vue **tidak bisa** mendeteksi perubahan pada array berikut:

1. Ketika kalian menentukan sebuah item dengan index, contohnya `vm.items[indexOfItem] = newValue`
2. Ketika kalian memodifikasi panjangnya sebuah array, contohnya `vm.items.length = newLength`

Sebagai contoh:

``` js
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // TIDAK bersifat reaktif
vm.items.length = 2 // TIDAK bersifat reaktif
```

Untuk mengatasi kekurangan 1, kedua kode berikut akan melakukan hal yang sama seperti `vm.items[indexOfItem] = newValue`, tetapi juga akan memicu perubahan status pada sistem reaktifitas:

``` js
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
```
``` js
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

Kalian juga bisa menggunakan metode instansi [`vm.$set`](https://vuejs.org/v2/api/#vm-set), yang mana adalah alias untuk `Vue.set` global:

``` js
vm.$set(vm.items, indexOfItem, newValue)
```

Untuk mengatasi kekurangan 2, kalian bisa menggunakan `splice`:

``` js
vm.items.splice(newLength)
```

## Kekurangan Pendeteksi perubahan objek

Karena batasan dari modern JavaScript lagi, **vue tidak bisa mendeteksi penambahan atau pengurangan properti**. Contohnya:

``` js
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` sekarang bersifat reactive

vm.b = 2
// `vm.b` tidak bersifat reactive
```

Vue tidak memperbolehkan menambahkan level-dasar properti reaktif secara dinamis ke instansi yang sudah dibuat. Tetapi, bisa untuk menambahkan properti reaktif ke objek terbungkus menggunakan metode `Vue.set(object, key, value)`. Sebagai contoh:

``` js
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

Kalian bisa menambahkan sebuah properti `age` baru ke objek yang dibungkus `userProfile` dengan:

``` js
Vue.set(vm.userProfile, 'age', 27)
```

Kalian juga bisa menggunakan metode instansi `vm.$set` , dimana sebagai alias untuk `Vue.set` yang global:

``` js
vm.$set(vm.userProfile, 'age', 27)
```

Terkadang kalian juga ingin menentukan angka sebagai properti baru ke objek yang sudah ada, contohnya menggunakan `Object.assign()` atau `_.extend()`. Di kasus seperti ini, kalian harus membuat sebuah objek baru dengan properti dari kedua objek. Jadi daripada membuat:

``` js
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

Kalian ingin menambahkan properti properti baru, dengan:

``` js
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## Menampilkan Hasil Penyaringan/Penyortiran

Terkadang kita ingin menampilkan versi array yang disaring atau disortir tanpa mengubah atau mengulang data yang asli. Pada kasus ini, kalian dapat membuat sebuah properti perhitungan yang mengembalikan array yang sudah di filter atau sortir.

Sebagai contoh:

``` html
<li v-for="n in evenNumbers">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

Pada situasi dimana properti yang dihitung tidak bisa diakses (contohnya didalam perulangan `v-for`), kalian bisa menggunakan:

``` html
<li v-for="n in even(numbers)">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## `v-for` dengan Batas

`v-for` jusa bisa menggunakan sebuah _integer_. Pada kasus ini perulangan akan melakukan perulangan pada _template_ sebanyak angka tersebut.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Hasil:

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
  new Vue({ el: '#range' })
</script>
{% endraw %}

## `v-for` pada `<template>`

Mirip dengan template `v-if`, kalian juga bisa menggunakan tag `<template>` dengan `v-for` untuk me-render beberapa blok elemen. Contohnya:

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```
## `v-for` dengan `v-if`

<p class="tip">Catatan bahwa **tidak** direkomendasikan untuk menggunakan `v-if` dan `v-for` bersamaan. Berdasar pada [panduan style](/v2/style-guide/#Avoid-v-if-with-v-for-essential) untuk detail.</p>

Ketika mereka ada pada node yang sama, `v-for` memiliki prioritas lebih tinggi dibandingkan `v-if`. Yang berarti `v-if` akan dijalankan pada setiap perulangan secara terpisah. Hal ini bisa berguna ketika kalian ingin me-render node untuk _sejumlah_ item, seperti dibawah ini:

``` html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

Diatas tadi hanya me-render _todo_ yang tidak selesai.

Tetapi jika, yang kalian inginkan untuk melewatkan eksekusi perulangan, kalian bisa menempatkan `v-if` pada elemen pembungkus (atau [`<template>`](conditional.html#Conditional-Groups-with-v-if-on-lt-template-gt)). Sebagai contoh:

``` html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

## `v-for` dengan sebuah Komponen

> Bagian ini mengasumsikan pengetahuan tentang [Komponen](components.html). Jangan ragu untuk melewatkannya dan kembali lain kali.

Kalian bisa menggunakan `v-for` pada komponen kustom, seperti elemen normal lainnya:

``` html
<my-component v-for="item in items" :key="item.id"></my-component>
```

> Pada 2.2.0+, ketika menggunakan `v-for` dengan sebuah komponen, sebuah [`key`](list.html#key) sekarang dibutuhkan.

Tetapi, hal ini tidak akan otomatis mengoper data ke komponen, karena komponen memiliki lingkup yang terbatas pada mereka sendiri. Agar mengoper data perulangan ke komponen, kita juga harus menggunakan _props_:

``` html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

Alasannya tidak otomatis memasukkan `item` ke komponen adalah karena hal itu membuat komponen tergabung erat dengan bagimana `v-for` bekerja. Menjadi eksplisit tentang dimana data datang membuat komponen tersebut dapat digunakan lagi pada situasi lain.

Berikut sebuah contoh lengkap daftar todo sederhana:

``` html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    >
    <button>Add</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
```

<p class="tip">Perhatikan attribut `is="todo-item"`. Hal ini sangat dibutuhkan untuk template DOM, karena hanya sebuah elemen `<li>` yang valid didalam sebuah `<ul>`. Hal tersebut berlaku sama dengan `<todo-item>`, tetapi sudah mengatasi browser error _parsing_ yang berpotensi. Lihat [Kekurangan Parsing Template DOM](components.html#DOM-Template-Parsing-Caveats) untuk mempelajari lebih lanjut.</p>

``` js
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Do the dishes',
      },
      {
        id: 2,
        title: 'Take out the trash',
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

{% raw %}
<div id="todo-list-example" class="demo">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    >
    <button>Add</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
<script>
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Do the dishes',
      },
      {
        id: 2,
        title: 'Take out the trash',
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
</script>
{% endraw %}
