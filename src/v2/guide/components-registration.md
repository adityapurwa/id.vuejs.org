---
title: Registrasi Komponen
type: guide
order: 101
---

> This page assumes you've already read the [Components Basics](components.html). Read that first if you are new to components.

## Nama Komponen

Ketika melakukan registrasi komponen, komponen harus selalu diberikan nama. Sebagai contoh, didalam mendaftarkan komponen di global kita sudah lihat sejauh ini
<!-- When registering a component, it will always be given a name. For example, in the global registration we've seen so far: -->

```js
Vue.component('nama-komponen', { /* ... */ })
```

Nama komponen adalah argumen pertama dari `Vue.component`.
<!-- The component's name is the first argument of `Vue.component`. -->

Nama yang diberikan akan tergantung dengan apa yang akan anda gunakan. Ketika menggunakan komponen langsung didalam DOM (sebagai perbandingan dengan template string atau [komponen-satu berkas](single-file-components.html)), kami merekomendasikan untuk mengikuti [Peraturan W3C](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) untuk membuat nama tag khusus (semua-huruf-harus-menggunakan-huruf-kecil, dan mengandung tanda penghubung). Ini bertujuan untuk menghindari konflik dengan elemen HTML sekarang atau nanti.

Anda dapat melihat rekomendasi lain untuk nama komponen di [Style Guide](../style-guide/#Base-component-names-strongly-recommended). 

<!-- The name you give a component may depend on where you intend to use it. When using a component directly in the DOM (as opposed to in a string template or [single-file component](single-file-components.html)), we strongly recommend following the [W3C rules](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) for custom tag names (all-lowercase, must contain a hyphen). This helps you avoid conflicts with current and future HTML elements. -->

<!-- You can see other recommendations for component names in the [Style Guide](../style-guide/#Base-component-names-strongly-recommended). -->

### Perkara Nama
<!-- ### Name Casing -->

Anda memiliki dua opsi ketika mendefinisikan nama komponen:
<!-- You have two options when defining component names: -->

#### Dengan kebab-case
<!-- #### With kebab-case -->

```js
Vue.component('menggunakan-kebab-case', { /* ... */ })
```

Ketika mendefinisikan komponen menggunakan kebab-case, anda harus menggunakan juga kebab-case ketika memberi referensi kostum elemen nya, seperti 
`<my-component-name>`.
<!-- When defining a component with kebab-case, you must also use kebab-case when referencing its custom element, such as in `<my-component-name>`. -->

#### Menggunakan PascalCase
<!-- #### With PascalCase -->

```js
Vue.component('NamaKomponenSaya', { /* ... */ })
```

Ketika mendefinisikan komponen menggunakan PascalCase, anda bisa menggunakan kedua nya ketika memberi referensi kostum elemen nya. Itu artinya kedua nya `<my-component-name>` dan `<MyComponentName>` dapat digunakan. Catatan, namun, hanya menggunakan nama kebab-case yang sah langsung di DOM (yaitu template non-string).
<!-- When defining a component with PascalCase, you can use either case when referencing its custom element. That means both `<my-component-name>` and `<MyComponentName>` are acceptable. Note, however, that only kebab-case names are valid directly in the DOM (i.e. non-string templates). -->

<!-- ## Global Registration -->
## Registrasi Komponen Secara Global

Sejauh ini, kita hanya membuat komponen menggunakan `Vue.Component`:
<!-- So far, we've only created components using `Vue.component`: -->

```js
Vue.component('nama-komponen', {
  // ... opsi ...
})
```

Komponen ini **teregistrasi secara global**. itu berarti mereka dapat digunakan di manapun didalam template Vue (`new Vue`) terbuat setelah terdaftar. Sebagai contoh:
<!-- These components are **globally registered**. That means they can be used in the template of any root Vue instance (`new Vue`) created after registration. For example: -->

```js
Vue.component('komponen-a', { /* ... */ })
Vue.component('komponen-b', { /* ... */ })
Vue.component('komponen-c', { /* ... */ })

new Vue({ el: '#app' })
```

```html
<div id="app">
  <komponen-a></komponen-a>
  <komponen-b></komponen-b>
  <komponen-c></komponen-c>
</div>
```

Ini bahkan berlaku untuk semua subkomponen, yang artinya ketiga komponen akan tersedia juga _satu sama lain_.
<!-- This even applies to all subcomponents, meaning all three of these components will also be available _inside each other_. -->

<!-- ## Local Registration -->
## Registrasi Komponen Secara Lokal

Registrasi secara global terkadang kurang ideal. sebagai contoh, jika anda menggunakan build system seperti Webpack, meregistrasi semua komponen secara global berarti meskipun anda tidak menggunakan komponen, komponen masih bisa dimasukkan  didalam final build. Yang secara tidak langsung akan meningkatkan jumlah Javascript yang akan diunduh oleh pengguna.
<!-- Global registration often isn't ideal. For example, if you're using a build system like Webpack, globally registering all components means that even if you stop using a component, it could still be included in your final build. This unnecessarily increases the amount of JavaScript your users have to download. -->

Didalam kasus ini, anda bisa mendefinisikan komponen sebagai obyek Javascript biasa:
<!-- In these cases, you can define your components as plain JavaScript objects: -->

```js
var KomponenA= { /* ... */ }
var KomponenB= { /* ... */ }
var KomponenC= { /* ... */ }
```

Lalu definisikan komponen yang akan anda gunakan di opsi `komponen`
<!-- Then define the components you'd like to use in a `components` option: -->

```js
new Vue({
  el: '#app',
  components: {
    'komponen-a': KomponenA,
    'komponen-b': KomponenB
  }
})
```

Untuk setiap properti di dalam obyek `components`, kunci akan menjadi nama kustom elemen, sementara nilai nya akan mengandung opsi dari obyek untuk komponen.
<!-- For each property in the `components` object, the key will be the name of the custom element, while the value will contain the options object for the component. -->

Dengan catatan **komponen yang terdaftar secara lokal _tidak akan tersedia_ di subkomponen**. Sebagai contoh, jika anda menginginkan `ComponentA` untuk tersedia di `KomponenB`, anda harus menggunakan
<!-- Note that **locally registered components are _not_ also available in subcomponents**. For example, if you wanted `ComponentA` to be available in `ComponentB`, you'd have to use: -->

```js
var KomponenA= { /* ... */ }

var KomponenB = {
  components: {
    'komponen-a': KomponenA
  },
  // ...
}
```

Atau jika anda menggunakan modul ES2015, seperti melalui Babel dan Webpack, mungkin akan terlihat seperti:
<!-- Or if you're using ES2015 modules, such as through Babel and Webpack, that might look more like: -->

```js
import KomponenA from './KomponenA.vue'

export default {
  components: {
    KomponenA
  },
  // ...
}
```

Dengan catatan di ES2015+, penempatan nama variabel seperti `KomponenA` didalam obyek singkatan untuk `KomponenA: KomponenA`, yang berarti nama variabel adalah keduanya
<!-- Note that in ES2015+, placing a variable name like `ComponentA` inside an object is shorthand for `ComponentA: ComponentA`, meaning the name of the variable is both: -->

- nama kustom elemen untuk digunakan di template, dan
- nama variable yang berisi opsi dari komponen
<!-- - the custom element name to use in the template, and -->
<!-- - the name of the variable containing the component options -->

<!-- ## Module Systems -->
## Sistem Modul

Jika anda tidak menggunakan sistem modul dengan `impor`/`require`, anda dapat melewatkan bagian ini untuk sekarang. Jika anda menggunakan nya, kami memiliki sebuah instruksi spesial dan tips untuk anda.
<!-- If you're not using a module system with `import`/`require`, you can probably skip this section for now. If you are, we have some special instructions and tips just for you. -->

<!-- ### Local Registration in a Module System -->
### Registrasi Lokal di Sistem Modul

Jika anda masih disini, maka kemungkinan besar anda menggunakan Sistem Modul, seperti dengan Babel dan Webpack. Didalam kasus ini, kami merekomendasikan membuat direktori `components`, masing-masing komponen didalam file sendiri.
<!-- If you're still here, then it's likely you're using a module system, such as with Babel and Webpack. In these cases, we recommend creating a `components` directory, with each component in its own file. -->

Dan anda perlu memasukan komponen-komponen yang akan anda gunakan, sebelum anda registrasi secara lokal. Sebagai contoh, Secara hipotesis `KomponenB.js` atau `KomponentB.vue` file:
<!-- Then you'll need to import each component you'd like to use, before you locally register it. For example, in a hypothetical `ComponentB.js` or `ComponentB.vue` file: -->

```js
import KomponenA from './KomponenA'
import KomponenC from './KomponenC'

export default {
  components: {
    KomponenA,
    KomponenC
  },
  // ...
}
```

Sekarang kedua nya `KomponenA` dan `KomponenC` dapat digunakan didalam template `KomponenB`.
<!-- Now both `ComponentA` and `ComponentC` can be used inside `ComponentB`'s template. -->

<!-- ### Automatic Global Registration of Base Components -->
### Registrasi Global Komponen Dasar Secara Otomatis

Komponen yang secara umum digunakan, boleh jadi hanya elemen seperti input ataupun tombol. Kami terkadang menyebutnya [komponen dasar](../style-guide/#Base-component-names-strongly-recommended) dan mereka terkadang sangat sering digunakan dikomponen anda.

<!-- Many of your components will be relatively generic, possibly only wrapping an element like an input or a button. We sometimes refer to these as [base components](../style-guide/#Base-component-names-strongly-recommended) and they tend to be used very frequently across your components. -->

<!-- The result is that many components may include long lists of base components: -->
Hasil nya kebanyakan komponen akan menyebabkan daftar yang panjang di komponen dasar:

```js
import BaseButton from './BaseButton.vue'
import BaseIcon from './BaseIcon.vue'
import BaseInput from './BaseInput.vue'

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput
  }
}
```

Hanya untuk menunjang markup yang relatif sedikit di template:
<!-- Just to support relatively little markup in a template: -->

```html
<BaseInput
  v-model="searchText"
  @keydown.enter="search"
/>
<BaseButton @click="search">
  <BaseIcon name="search"/>
</BaseButton>
```

Beruntungnya, jika anda menggunakan Webpack (atau [Vue CLI 3+](https://github.com/vuejs/vue-cli), yang menggunakan Webpack didalam nya), anda bisa menggunakan `require.context` untuk meregistrasi beberapa komponen yang sangat umum ini secara global. Berikut ini adalah contoh kode untuk menambahkan komponen dasar secara global di dalam aplikasi anda (mis `src/main.js`):
<!-- Fortunately, if you're using Webpack (or [Vue CLI 3+](https://github.com/vuejs/vue-cli), which uses Webpack internally), you can use `require.context` to globally register only these very common base components. Here's an example of the code you might use to globally import base components in your app's entry file (e.g. `src/main.js`): -->

```js
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // Path relatif folder komponen
  './components',
  // Untuk melihat ataupun tidak melihat di subfolder
  false,
  // Regular Expression digunakan untuk mencocokkan nama file komponen
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // Mendapatkan konfigurasi komponen
  const componentConfig = requireComponent(fileName)

  // Mendapatkan nama komponen yang menggunakan PascalCase
  const componentName = upperFirst(
    camelCase(
      // Mendapatkan nama file terlepas dari dalam nya folder
      fileName
        .split('/')
        .pop()
        .replace(/\.\w+$/, '')
    )
  )


  // Registrasi Komponen Secara Global
  Vue.component(
    componentName,
    // Melihat opsi komponen di `.default`, yang mana akan tersedia jika komponen telah diekspor dengan `export default`,
    // Sebaliknya akan kembali ke sumber modul
    componentConfig.default || componentConfig
  )
})
```

Perlu di ingat **registrasi secara global harus dilakukan sebelum Vue instance dibuat (dengan `new Vue`)**. [Berikut ini adalah contoh](https://github.com/chrisvfritz/vue-enterprise-boilerplate/blob/master/src/components/_globals.js) dari pola ini di dalam sebuah konteks project sesungguhnya.
