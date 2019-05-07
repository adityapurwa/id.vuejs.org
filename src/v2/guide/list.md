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
    {{ item.pesan }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { pesan: 'Foo' },
      { pesan: 'Bar' }
    ]
  }
})
```

Result:

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items">
    {{item.pesan}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { pesan: 'Foo' },
      { pesan: 'Bar' }
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

Didalam blok `v-for` kita memiliki akses penuh kepada cangkupan propery _parent_. `v-for` juga mendukung sebuah argumen opsional kedua untuk index dari item saat ini.

``` html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ pesanParent }} - {{ index }} - {{ item.pesan }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    pesanParent: 'Parent',
    items: [
      { pesan: 'Foo' },
      { pesan: 'Bar' }
    ]
  }
})
```

Result:

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="(item, index) in items">
    {{ pesanParent }} - {{ index }} - {{ item.pesan }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    PesanParent: 'Parent',
    items: [
      { pesan: 'Foo' },
      { pesan: 'Bar' }
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

## Array Change Detection

### Mutation Methods

Vue wraps an observed array's mutation methods so they will also trigger view updates. The wrapped methods are:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

You can open the console and play with the previous examples' `items` array by calling their mutation methods. For example: `example1.items.push({ message: 'Baz' })`.

### Replacing an Array

Mutation methods, as the name suggests, mutate the original array they are called on. In comparison, there are also non-mutating methods, e.g. `filter()`, `concat()` and `slice()`, which do not mutate the original array but **always return a new array**. When working with non-mutating methods, you can replace the old array with the new one:

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

You might think this will cause Vue to throw away the existing DOM and re-render the entire list - luckily, that is not the case. Vue implements some smart heuristics to maximize DOM element reuse, so replacing an array with another array containing overlapping objects is a very efficient operation.

### Caveats

Due to limitations in JavaScript, Vue **cannot** detect the following changes to an array:

1. When you directly set an item with the index, e.g. `vm.items[indexOfItem] = newValue`
2. When you modify the length of the array, e.g. `vm.items.length = newLength`

For example:

``` js
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // is NOT reactive
vm.items.length = 2 // is NOT reactive
```

To overcome caveat 1, both of the following will accomplish the same as `vm.items[indexOfItem] = newValue`, but will also trigger state updates in the reactivity system:

``` js
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
```
``` js
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

You can also use the [`vm.$set`](https://vuejs.org/v2/api/#vm-set) instance method, which is an alias for the global `Vue.set`:

``` js
vm.$set(vm.items, indexOfItem, newValue)
```

To deal with caveat 2, you can use `splice`:

``` js
vm.items.splice(newLength)
```

## Object Change Detection Caveats

Again due to limitations of modern JavaScript, **Vue cannot detect property addition or deletion**. For example:

``` js
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` is now reactive

vm.b = 2
// `vm.b` is NOT reactive
```

Vue does not allow dynamically adding new root-level reactive properties to an already created instance. However, it's possible to add reactive properties to a nested object using the `Vue.set(object, key, value)` method. For example, given:

``` js
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

You could add a new `age` property to the nested `userProfile` object with:

``` js
Vue.set(vm.userProfile, 'age', 27)
```

You can also use the `vm.$set` instance method, which is an alias for the global `Vue.set`:

``` js
vm.$set(vm.userProfile, 'age', 27)
```

Sometimes you may want to assign a number of new properties to an existing object, for example using `Object.assign()` or `_.extend()`. In such cases, you should create a fresh object with properties from both objects. So instead of:

``` js
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

You would add new, reactive properties with:

``` js
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## Displaying Filtered/Sorted Results

Sometimes we want to display a filtered or sorted version of an array without actually mutating or resetting the original data. In this case, you can create a computed property that returns the filtered or sorted array.

For example:

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

In situations where computed properties are not feasible (e.g. inside nested `v-for` loops), you can use a method:

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

## `v-for` with a Range

`v-for` can also take an integer. In this case it will repeat the template that many times.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Result:

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
  new Vue({ el: '#range' })
</script>
{% endraw %}

## `v-for` on a `<template>`

Similar to template `v-if`, you can also use a `<template>` tag with `v-for` to render a block of multiple elements. For example:

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` with `v-if`

<p class="tip">Note that it's **not** recommended to use `v-if` and `v-for` together. Refer to [style guide](/v2/style-guide/#Avoid-v-if-with-v-for-essential) for details.</p>

When they exist on the same node, `v-for` has a higher priority than `v-if`. That means the `v-if` will be run on each iteration of the loop separately. This can be useful when you want to render nodes for only _some_ items, like below:

``` html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

The above only renders the todos that are not complete.

If instead, your intent is to conditionally skip execution of the loop, you can place the `v-if` on a wrapper element (or [`<template>`](conditional.html#Conditional-Groups-with-v-if-on-lt-template-gt)). For example:

``` html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

## `v-for` with a Component

> This section assumes knowledge of [Components](components.html). Feel free to skip it and come back later.

You can directly use `v-for` on a custom component, like any normal element:

``` html
<my-component v-for="item in items" :key="item.id"></my-component>
```

> In 2.2.0+, when using `v-for` with a component, a [`key`](list.html#key) is now required.

However, this won't automatically pass any data to the component, because components have isolated scopes of their own. In order to pass the iterated data into the component, we should also use props:

``` html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

The reason for not automatically injecting `item` into the component is because that makes the component tightly coupled to how `v-for` works. Being explicit about where its data comes from makes the component reusable in other situations.

Here's a complete example of a simple todo list:

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

<p class="tip">Note the `is="todo-item"` attribute. This is necessary in DOM templates, because only an `<li>` element is valid inside a `<ul>`. It does the same thing as `<todo-item>`, but works around a potential browser parsing error. See [DOM Template Parsing Caveats](components.html#DOM-Template-Parsing-Caveats) to learn more.</p>

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
