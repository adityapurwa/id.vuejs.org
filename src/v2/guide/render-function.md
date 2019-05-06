---
title: Fungsi _Render_ & JSX
type: guide
order: 303
---

## Dasar

Pada banyak kasus, Vue menyarankan penggunaan templat untuk membuat HTML. Tetapi, ada beberapa situasi di mana kalian butuh menggunakan JavaScript dengan penuh. Kalian bisa menggunakan **fungsi render**, sebagai alternatif dari templat.

Lihat contoh sederhana di bawah ini di mana fungsi `render` bermanfaat. Katakanlah kalian ingin menghasilkan _anchored heading_ (heading dengan anchor di dalamnya):

``` html
<h1>
  <a name="hello-world" href="#hello-world">
    Hello world!
  </a>
</h1>
```
Dari HTML di atas, kalian ingin menggunakan komponen di atas seperti:

``` html
<anchored-heading :level="1">Hello world!</anchored-heading>
```

Ketika kalian mulai membuat heading berdasarkan properti `level`, hasilnya akan seperti ini:

``` html
<script type="text/x-template" id="anchored-heading-template">
  <h1 v-if="level === 1">
    <slot></slot>
  </h1>
  <h2 v-else-if="level === 2">
    <slot></slot>
  </h2>
  <h3 v-else-if="level === 3">
    <slot></slot>
  </h3>
  <h4 v-else-if="level === 4">
    <slot></slot>
  </h4>
  <h5 v-else-if="level === 5">
    <slot></slot>
  </h5>
  <h6 v-else-if="level === 6">
    <slot></slot>
  </h6>
</script>
```

``` js
Vue.component('anchored-heading', {
  template: '#anchored-heading-template',
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

Templat di atas terlihat rumit, dan banyak sekali duplikasi di dalamnya seperti penggunaan `<slot></slot>` di setiap level heading.

Dalam kasus ini, penggunaan templat (meskipun bekerja dengan baik) kurang tepat. Mari kita coba tulis ulang dengan menggunakan fungsi `render`:

``` js
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   // nama tagnya
      this.$slots.default // isi dari komponennya
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

Lebih sederhana! kodenya lebih pendek, tapi kalian harus terbiasa dengan properti yang ada di instan Vue. Dalam kasus di atas, kalian harus tahu bahwa isi dari komponen yang tidak ditandai direktif `v-slot`, seperti teks `Hello world!` di dalam `anchored-heading`, isi tersebut disimpan di `$slots.default`. Jika kalian ingin belajar lebih lanjut seputar ini, **direkomendasikan untuk membaca [API properti instan](../api/#Instance-Properties)** sebelum lebih dalam belajar tentang fungsi render.

## _Node_, _Tree_, dan _Virtual DOM_

Sebelum lanjut lebih dalam tentang fungsi render, penting untuk sedikit mengetahui tentang bagaimana _browser_ bekerja. Lihat contoh HTML di bawah:

```html
<div>
  <h1>My title</h1>
  Some text content
  <!-- TODO: Add tagline  -->
</div>
```

Ketika browser membaca kode di atas, browser akan membuat pohon yang terdiri dari _DOM nodes_ (https://javascript.info/dom-nodes) untuk membantu mencatat isi HTML tersebut, sama seperti jika kalian membuat pohon silsilah keluarga untuk mencatat isi dari keluarga kalian.

Bentuk pohon dari DOM nodes untuk HTML di atas terlihat seperti:

![Visualisasi DOM Tree](/images/dom-tree.png)

Setiap element adalah node. Setiap teks adalah node. Bahkan komentar juga node! Sama seperti silsilah keluarga, node juga bisa memiliki anak / isi di dalamnya.

Memperbarui node yang banyak ini secara efisien tidaklah mudah, tapi untungnya, kalian tidak perlu melakukannya secara manual. Sebaliknya, gunakan Vue untuk membentuk HTML yang kalian inginkan melalui templat:

```html
<h1>{{ blogTitle }}</h1>
```

Atau fungsi render:

``` js
render: function (createElement) {
  return createElement('h1', this.blogTitle)
}
```

Dalam kedua kasus, Vue secara otomatis akan memastikan bahwa tampilannya diperbarui dengan benar, seperti ketika `blogTitle` berubah.

### Virtual DOM

Vue melakukannya dengan cara membuat **virtual DOM** yang digunakan untuk mencatat perubahan yang dibutuhkan untuk merekonstruksi DOM yang sesungguhnya. Lihat baris kode di bawah:

``` js
return createElement('h1', this.blogTitle)
```

Yang dikembalikan `createElement` bukanlah elemen DOM yang benar-benar diproses oleh browser. Secara teknis, lebih akurat jika dinamakan `createNodeDescription`, karena berisi informasi yang dibutuhkan oleh Vue, jenis node apa yang harus ditampilkan; termasuk deskripsi ini dari node tersebut. Deskripsi node ini diistilahkan dengan "virtual node", biasanya disingkat **VNode**. Sedangkan "Virtual DOM" digunakan untuk merujuk ke kumpulan dari VNode secara keseluruhan.

## Argumen `createElement`

Untuk lebih mendalami penggunaan fitur templat di fungsi `createElement`, berikut beberapa argumen yang diterima oleh `createElement`:

``` js
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // Nama tag HTML, opsi komponen, atau fungsi asinkronus yang mengembalikan nilai tag HTML atau opsi komponen
  // Wajib.
  'div',

  // {Object}
  // Objek data seperti yang digunakan di templat.
  // Opsional.
  {
    // (lihat detail di bagian selanjutnya)
  },

  // {String | Array}
  // Isi VNode, bisa dengan `createElement()`,
  // atau string sepert 'text VNodes'.
  // Opsional.
  [
    'Some text comes first.',
    createElement('h1', 'A headline'),
    createElement(MyComponent, {
      props: {
        someProp: 'foobar'
      }
    })
  ]
)
```

### Lebih Dalam Objek Data

Penting untuk dicatat, sama halnya `v-bind:class` dan `v-bind:style` diperlakukan secara khusus di templat, mereka punya tempat tersendiri juga di objek data dari VNode. Lewat objek ini kalian juga melakukan proses _binding_ ke atribut bawaan HTML juga properti DOM seperti `innerHTML` (menggantikan direktif `v-html`):

``` js
{
  // Sama seperti `v-bind:class`, bisa diisi dengan
  // string, objek, atau array dari string dan objek
  class: {
    foo: true,
    bar: false
  },
  // Sama seperti `v-bind:style`, bisa diisi dengan
  // string, objek, atau array dari objek
  style: {
    color: 'red',
    fontSize: '14px'
  },
  // Atribut bawaan HTML
  attrs: {
    id: 'foo'
  },
  // properti komponen
  props: {
    myProp: 'bar'
  },
  // properti DOM
  domProps: {
    innerHTML: 'baz'
  },
  // Event handler diletakkan di dalam `on`, meskipun
  // kalian tidak akan bisa menggunakan modifier seperti `v-on:keyup.enter`.  
  // Kalian harus melakukan pemeriksaan manual untuk keycodenya.
  on: {
    click: this.clickHandler
  },
  // Hanya untuk komponen, digunakan untuk memperhatikan
  // event bawaan, selain dari event yang dihasilkan oleh `vm.$emit`.  
  nativeOn: {
    click: this.nativeClickHandler
  },
  // Direktif kustom. Ingat bahwa `oldValue` tidak bisa diisi secara manual
  // Karena Vue akan mengaturnya sendiri.
  directives: [
    {
      name: 'my-custom-directive',
      value: '2',
      expression: '1 + 1',
      arg: 'foo',
      modifiers: {
        bar: true
      }
    }
  ],
  // Scoped slots dalam bentuk
  // { name: props => VNode | Array<VNode> }
  scopedSlots: {
    default: props => createElement('span', props.text)
  },
  // Nama dari slot, jika komponen ini anak dari komponen lain  
  slot: 'name-of-slot',
  // Properti khusus lainnya
  key: 'myKey',
  ref: 'myRef',
  // Jika kalian menggunakan nama ref yang sama di beberapa tempat, nilai dari `$refs.myRef` akan berbentuk array
  refInFor: true
}
```

### Contoh Lengkap

Dengan informasi di atas, kita bisa melanjutkan komponen yang tadi kita buat:

``` js
var getChildrenTextContent = function (children) {
  return children.map(function (node) {
    return node.children
      ? getChildrenTextContent(node.children)
      : node.text
  }).join('')
}

Vue.component('anchored-heading', {
  render: function (createElement) {
    // create kebab-case id
    var headingId = getChildrenTextContent(this.$slots.default)
      .toLowerCase()
      .replace(/\W+/g, '-')
      .replace(/(^-|-$)/g, '')

    return createElement(
      'h' + this.level,
      [
        createElement('a', {
          attrs: {
            name: headingId,
            href: '#' + headingId
          }
        }, this.$slots.default)
      ]
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

### Keterbatasan

#### VNode harus unik

Semua VNode yang ada di silsilah pohon komponen harus unik. Artinya, contoh di bawah salah:

``` js
render: function (createElement) {
  var myParagraphVNode = createElement('p', 'hi')
  return createElement('div', [
    // myParagraphVNode digunakan dua kali!
    myParagraphVNode, myParagraphVNode
  ])
}
```

Jika kalian memang ingin menduplikasi elemen/komponen yang sama beberapa kali, kalian bisa menggunakan fungsi _factory_. Di bawah ini adalah contoh yang valid untuk menampilkan 20 paragraf yang sama:

``` js
render: function (createElement) {
  return createElement('div',
    Array.apply(null, { length: 20 }).map(function () {
      return createElement('p', 'hi')
    })
  )
}
```

## Mengganti Fitur Templat Dengan JavaScript

### `v-if` dan `v-for`

Jika ada hal sederhana yang bisa dicapai dengan mudah menggunakan JavaScript, fungsi render Vue tidak menyediakan cara khusus untuk melakukannya. Contohnya, penggunaan `v-if` dan `v-for` di templat:

``` html
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```

Bisa ditulis dengan `if`/`else` dan `map` bawaan dari JavaScript:

``` js
props: ['items'],
render: function (createElement) {
  if (this.items.length) {
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name)
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
```

### `v-model`

Tidak ada fitur yang mirip `v-model` di fungsi render, kalian harus menerapkannya sendiri:

``` js
props: ['value'],
render: function (createElement) {
  var self = this
  return createElement('input', {
    domProps: {
      value: self.value
    },
    on: {
      input: function (event) {
        self.$emit('input', event.target.value)
      }
    }
  })
}
```

Mungkin terasa lebih susah, tapi kalian punya kendali penuh dibandingkan `v-model`.

### _Event_ & _Key Modifier_

Untuk modifier event seperti `.passive`, `.capture`, dan `.once`; Vue memberikan prefix yang bisa digunakan dengan `on`:

| Modifier(s) | Prefix |
| ------ | ------ |
| `.passive` | `&` |
| `.capture` | `!` |
| `.once` | `~` |
| `.capture.once` atau<br>`.once.capture` | `~!` |

Contoh:

```javascript
on: {
  '!click': this.doThisInCapturingMode,
  '~keyup': this.doThisOnce,
  '~!mouseover': this.doThisOnceInCapturingMode
}
```

Untuk event dan key modifier lainnya, tidak ada prefix yang dibutuhkan, cukup gunakan event method-nya di handler:

| Modifier(s) | Persamaan yang bisa digunakan di handler |
| ------ | ------ |
| `.stop` | `event.stopPropagation()` |
| `.prevent` | `event.preventDefault()` |
| `.self` | `if (event.target !== event.currentTarget) return` |
| Key:<br>`.enter`, `.13` | `if (event.keykode !== 13) return` (ganti `13` ke [kode key lain](http://keykode.info/) untuk key modifier lainnya) |
| Modifier Key:<br>`.ctrl`, `.alt`, `.shift`, `.meta` | `if (!event.ctrlKey) return` (ganti `ctrlKey` ke `altKey`, `shiftKey`, atau `metaKey`, sesuaikan saja) |

Berikut contoh untuk penggunaan modifier di atas:

```javascript
on: {
  keyup: function (event) {
    // Batalkan jika elemen yang menghasilkan event ini bukan
    // elemen tempat event-nya di pasang
    if (event.target !== event.currentTarget) return
    // Batalkan jika key yang ditekan bukanlah tombol enter,
    // dan tombol shift tidak ditekan di waktu yang sama
    if (!event.shiftKey || event.keykode !== 13) return
    // Hentikan propagasi event
    event.stopPropagation()
    // Hentikan event handler dasar untuk event ini
    event.preventDefault()
    // ...
  }
}
```

### Slot

Kalian bisa mengakses isi slot statik sebagai array VNode dari [`this.$slots`](../api/#vm-slots):

``` js
render: function (createElement) {
  // `<div><slot></slot></div>`
  return createElement('div', this.$slots.default)
}
```

Dan mengakses slot berlingkup (scoped) sebagai fungsi yang mengembalikan VNode dari [`this.$scopedSlots`](../api/#vm-scopedSlots):

``` js
props: ['message'],
render: function (createElement) {
  // `<div><slot :text="message"></slot></div>`
  return createElement('div', [
    this.$scopedSlots.default({
      text: this.message
    })
  ])
}
```

Untuk meneruskan slot berlingkup ke komponen di dalamnya melalui fungsi render, gunakan field `scopedSlots` di data VNode:

``` js
render: function (createElement) {
  return createElement('div', [
    createElement('child', {
      // teruskan `scopedSlots` di objek data
      // dalam bentuk { name: props => VNode | Array<VNode> }
      scopedSlots: {
        default: function (props) {
          return createElement('span', props.text)
        }
      }
    })
  ])
}
```

## JSX

Jika kalian menulis fungsi render yang panjang, akan menyusahkan jika kalian harus menulis seperti di bawah:

``` js
createElement(
  'anchored-heading', {
    props: {
      level: 1
    }
  }, [
    createElement('span', 'Hello'),
    ' world!'
  ]
)
```

Terutama jika versi templatnya lebih sederhana:

``` html
<anchored-heading :level="1">
  <span>Hello</span> world!
</anchored-heading>
```

Karena itulah ada [plugin Babel](https://github.com/vuejs/jsx) untuk menggunakan JSX dengan Vue, memberikan kita cara penulisan yang sedikit mirip templat:

``` js
import AnchoredHeading from './AnchoredHeading.vue'

new Vue({
  el: '#demo',
  render: function (h) {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})
```

<p class="tip">Menamai `createElement` menjadi `h` adalah aturan umum yang akan kalian sering temukan di ekosistem Vue dan wajib dilakukan untuk menggunakan JSX. Mulai dari plugin Babel untuk Vue [versi 3.4.0](https://github.com/vuejs/babel-plugin-transform-vue-jsx#h-auto-injection), secara otomatis diberikan baris `const h = this.$createElement` di method dan getter apa saja (selama bukan fungsi atau fungsi panah / _arrow function_), yang dideklarasikan di penulisan kode ES20155 yang berisi JSX, sehingga kalian bisa menghapus bagian parameter `(h)`. Di versi sebelumnya, akan terjadi error jika tidak ada `h` yang tersedia di lingkup tersebut.</p>

Untuk pelajari lebih lanjut tentang JSX, lihat [dokumentasi penggunaannya](https://github.com/vuejs/jsx#installation).

## Komponen Fungsional

Komponen anchored heading yang kita buat sebelumnya relatif sederhana. Tidak ada state, watcher, dan tidak ada method lifecycle. Hanya sebuah fungsi dengan beberapa props.

Dalam kasus ini, kita bisa menandai komponen ini dengan `functinoal`, artinya mereka tidak memiliki state (tidak ada [reactive data](../api/#Options-Data)) dan tanpa instan (tidak ada konteks `this`). **Komponen fungsional** terlihat seperti ini:

``` js
Vue.component('my-component', {
  functional: true,
  // Props tidak wajib
  props: {
    // ...
  },
  // Karena tidak ada instan,
  // konteks diteruskan melalui parameter kedua
  render: function (createElement, context) {
    // ...
  }
})
```

> Catatan: di versi sebelum 2.3.0, opsi `props` harus ditulis jika kalian ingin komponen fungsional kalian menerima props. Di versi 2.3.0 ke atas, kalian tidak perlu menulis opsi `props` dan semua atribut yang ada di node komponen tersebut akan secara otomatis diambil sebagai props.

Di versi 2.5.0 ke atas, jika kalian menggunakan [kopmonen satu berkas / single-file components](single-file-components.html), komponen fungsional yang menggunakan templat bisa ditulis seperti di bawah:

``` html
<template functional>
</template>
```

Semua yang dibutuhkan oleh komponen akan diteruskan melalui `context`, sebuah objek yang berisi:

- `props`: objek berisi props
- `children`: Array VNode yang berisi anak dari komponen tersebut
- `slots`: Fungsi yang mengembalikan objek slot
- `scopedSlots`: (2.6.0+) Objek yang berisi slot berlingkup. Juga menyediakan slot normal sebagai fungsi.
- `data`: [data objek](#The-Data-Object-In-Depth), diteruskan ke komponen sebagai argumen kedua dari `createElement`
- `parent`: Referensi ke komponen di atasnya
- `listeners`: (2.3.0+) Objek yang berisi event listener yang didaftarkan oleh komponen di atasnya. Juga sebagai alias untuk `data.on`
- `injections`: (2.3.0+) jika ada opsi [`inject`](../api/#provide-inject), akan berisi injeksi yang ditemukan/diresolve.

Setelah menuliskan `functional: true`, pembaharuan fungsi render dari komponen anchored heading yang baru dibuat mengharuskan kalian menambahkan argumen `context`, merubah `this.$slots.default` menjadi `context.children`, lalu merubah `this.level` menjadi `context.props.level`.

Karena komponen fungsional hanya sebuah fungsi saja, mereka lebih cepat untuk dirender. Tetapi, karena tidak adanya instan yang bisa dijadikan acuan, komponen fungsional tidak akan muncul di pohon komponen [Vue devtools](https://github.com/vuejs/vue-devtools).

Mereka sangat bermanfaat jika digunakan sebagai komponen pembungkus / _wrapper_. Contohnya:

- Secara program memilih beberapa komponen yang akan didelegasikan
- Memanipulasi isi, props, dan data sebelum meneruskannya ke komponen lain di dalamnya

Berikut adalah contoh untuk komponen `smart-list` yang akan mengembalikan komponen yang lebih spesifik, tergantung dari props yang diberikan:

``` js
var EmptyList = { /* ... */ }
var TableList = { /* ... */ }
var OrderedList = { /* ... */ }
var UnorderedList = { /* ... */ }

Vue.component('smart-list', {
  functional: true,
  props: {
    items: {
      type: Array,
      required: true
    },
    isOrdered: Boolean
  },
  render: function (createElement, context) {
    function appropriateListComponent () {
      var items = context.props.items

      if (items.length === 0)           return EmptyList
      if (typeof items[0] === 'object') return TableList
      if (context.props.isOrdered)      return OrderedList

      return UnorderedList
    }

    return createElement(
      appropriateListComponent(),
      context.data,
      context.children
    )
  }
})
```

### Meneruskan Atribut dan Event ke Anak Komponen

Umumnya, atribut yang tidak didefinisikan sebagai prop akan otomatis ditambahkan ke elemen akar dari komponen tersebut, menindih atau [menggabungkan dengan seksama](class-and-style.html) atribut yang sudah ada dengan nama yang sama.

Tetapi untuk komponen fungsional, kalian harus secara tertulis mendefinisikannya:

```js
Vue.component('my-functional-button', {
  functional: true,
  render: function (createElement, context) {
    // Teruskan atribut, event listener, anak, dan sebagainya.
    return createElement('button', context.data, context.children)
  }
})
```

Dengan meneruskan `context.data` sebagai argumen kedua dari `createElement`, kita juga meneruskan atribut dan event lain yang digunakan di `my-functional-button`. Kalian tidak butuh menambahkan modifier `.native`.

Jika kalian menggunakan komponen fungsional berbasis templat, kalian harus meneruskan atribut dan listener secara manual. Kalian bisa menggunakan `data.attrs` untuk meneruskan atribut HTML, dan `listeners` _(alias dari `data.on`)_ untuk meneruskan event listener.

```html
<template functional>
  <button
    class="btn btn-primary"
    v-bind="data.attrs"
    v-on="listeners"
  >
    <slot/>
  </button>
</template>
```

### `slots()` vs `children`

Jika kalian penasaran kenapa kita butuh `slots()` dan `children`. Dan bukankah `slots().default` sama dengan `children`? Di beberapa kasus, iya - tapi bagaimana jika kita punya komponen fungsional seperti di bawah?

``` html
<my-functional-component>
  <p v-slot:foo>
    first
  </p>
  <p>second</p>
</my-functional-component>
```

Untuk komponen ini, `children` akan mengembalikan kedua paragraf; sedangkan `slots().default` akan mengembalikan yang kedua saja, dan `slots().foo` akan mengembalikan yang pertama saja. Adanya `children` dan `slots()` memberi keluasan untuk memilih apakah komponen yang kita buat tahu tentang slot yang kita gunakan, atau mungkin hanya ingin meneruskan masalah pemilihan slot tersebut ke komponen lain sembari juga meneruskan `children`.

## Kompilasi Templat

Kalian mungkin penasaran, bagaimana caranya templat Vue bisa dirubah menjadi fungsi render. Detail implementasi ini tidak wajib untuk diketahui. Tapi jika kalian penasaran, di bawah ini adalah demo menggunakan `Vue.compile` untuk secara langsung merubah string templat menjadi fungsi render:

{% raw %}
<div id="vue-compile-demo" class="demo">
  <textarea v-model="templateText" rows="10"></textarea>
  <div v-if="typeof result === 'object'">
    <label>render:</label>
    <pre><kode>{{ result.render }}</kode></pre>
    <label>staticRenderFns:</label>
    <pre v-for="(fn, index) in result.staticRenderFns"><kode>_m({{ index }}): {{ fn }}</kode></pre>
    <pre v-if="!result.staticRenderFns.length"><kode>{{ result.staticRenderFns }}</kode></pre>
  </div>
  <div v-else>
    <label>Compilation Error:</label>
    <pre><kode>{{ result }}</kode></pre>
  </div>
</div>
<script>
new Vue({
  el: '#vue-compile-demo',
  data: {
    templateText: '\
<div>\n\
  <header>\n\
    <h1>I\'m a template!</h1>\n\
  </header>\n\
  <p v-if="message">\n\
    {{ message }}\n\
  </p>\n\
  <p v-else>\n\
    No message.\n\
  </p>\n\
</div>\
    ',
  },
  computed: {
    result: function () {
      if (!this.templateText) {
        return 'Enter a valid template above'
      }
      try {
        var result = Vue.compile(this.templateText.replace(/\s{2,}/g, ''))
        return {
          render: this.formatFunction(result.render),
          staticRenderFns: result.staticRenderFns.map(this.formatFunction)
        }
      } catch (error) {
        return error.message
      }
    }
  },
  methods: {
    formatFunction: function (fn) {
      return fn.toString().replace(/(\{\n)(\S)/, '$1  $2')
    }
  }
})
console.error = function (error) {
  throw new Error(error)
}
</script>
<style>
#vue-compile-demo {
  -webkit-user-select: inherit;
  user-select: inherit;
}
#vue-compile-demo pre {
  padding: 10px;
  overflow-x: auto;
}
#vue-compile-demo kode {
  white-space: pre;
  padding: 0
}
#vue-compile-demo textarea {
  width: 100%;
  font-family: monospace;
}
</style>
{% endraw %}
