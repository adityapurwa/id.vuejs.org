---
title: Perbandingan dengan framework lainnya
type: guide
order: 801
---

Panduan ini merupakan panduan yang paling susah untuk ditulis, tapi kami rasa sangat penting. Kemungkinannya adalah, kalian memiliki masalah yang kalian coba pecahkan dan kalian menggunakan library lain untuk memecahkannya. Kalian disini karena kalian penasaran apakah Vue dapat memecahkan masalah spesifik dengan cara yang lebih baik. Hal itulah yang kami harapkan untuk menjawab kalian.

Kami juga berusaha sangat keras untuk menghindari penyimpangan. Sebagai tim utama, tentu saja kami sangat menyukai Vue. Kami pikir terdapat beberapa masalah yang diselesaikan secara lebih baik daripada semua hal di luar sana. Jika kami tidak mempercayai hal tersebut, kami tidak akan mengerjakan hal tersebut. Walaupun kami berkeinginan untuk jujur dan akurat. Dimana library lain menawarkan keunggulan tersendiri, contohnya ekosistem penyaji alternative yang luas atau dukungan browser jauh kebelakang hingga IE6, kami juga mencoba untuk mendaftar hal tersebut.

Kami juga menyukai bantuan **kalian** dalam menjaga dokumen ini mutakhir karena dunia JavaScript bergerak sangat cepat! Jika kalian menemukan sesuatu yang tidak akurat atau sesuatu yang terlihat tidak benar, tolong beritahu kami dengan cara [membuka issue](https://github.com/vuejs/vuejs.org/issues/new?title=Inaccuracy+in+comparisons+guide).

## React

React dan Vue memiliki banyak kesamaan. Mereka berdua:

- memanfaatkan DOM virtual
- menyediakan tampilan komponen reaktif dan mudah disusun
- Mempertahankan fokus di library inti, dengan perhatian terhadap perutean dan manajemen keadaan global yang ditangani oleh library pendamping.

Menjadi sangat mirip dalam lingkupan, kami menempatkan lebih banyak waktu untuk merapikan perbandingan ini daripada yang lain. Kami ingin memastikan tidak hanya keakuratan teknis, tetapi juga keseimbangan. Kami menonjolkan dimana kelebihan React dibanding Vue, contohnya ada pada kekayaan di ekosistemnya dan melimpahnya penyaji kustom mereka.

Dengan mempertimbangkan hal tersebut, tidak dapat dihindari bahwa perbandingan akan terlihat menonjol kearah Vue kepada beberapa pengguna React, karena banyak dari subjek yang dieksplorasi lebih menjangkau subjektif. Kami mengakui keberadaan selera yang beragam, dan perbandingan ini semula bertujuan untuk menggaris besarkan alasan mengapa Vue bisa berpotensi lebih cocok jika preferensi selera mu ternyata berlaku dengan kami juga.

Beberapa dari bagian di bawah bisa jadi sedikit usang karena pembaruan React 16+, dan kami berencana untuk bekerja sama dengan komunitas React untuk merubah bagian ini pada waktu dekat.

### Performa Kecepatan

React dan Vue keduanya dapat terbilang dang memiliki kecepatan yang sama, jadi kecepatan tidak memungkinkan dijadikan sebagai faktor penentuan diantara mereka. Kemudian untuk perhitungan spesifik, lihat [tolak ukur pihak ketiga](https://stefankrause.net/js-frameworks-benchmark8/table.html),  dimana berfokus kepada penyajian / pembaruan performa mentah dengan pokok komponen sederhana.

#### Usaha Optimisasi

Di React, ketika keadaan komponen berubah, komponen tersebut memicu penyajian ulang seluruh turunan komponen, dimulai dari dasar komponen tersebut. Untuk menghindari penyajian ulang yang tidak dibutuhkan dari komponen turunan, kalian setidaknya membutuhkan `PureComponent` atau mengimplementasikan `shouldComponentUpdate` ketika kalian bisa. Kalian juga dapat menggunakan struktur data yang tidak dapat dimutasi untuk menjadikan keadaan perubahan kalian lebih berteman dengan optimisasi. Bagaimanapun juga, pada kasus tertentu kalian juga tidak dapat berharap pada optimisasi karena `PureComponent/shouldComponentUpdate` menganggap keseluruhan penyajian turunan pokok ditentukan dari penopang dari komponent saat ini. Jika hal tersebut bukanlah kasusnya, optimisasi tersebut malah merujuk kearah keadaan DOM yang tidak konsisten.

Di Vue, sebuah dependensi komponen akan dilacak secara otomatis ketika disajikan, jadi sistem akan mengetahui persis komponen mana yang perlu di sajikan ulang ketika suatu keadaan berubah. Setiap komponen bisa dianggap mempunyai `shouldComponentUpdate` diimplementasikan secara otomatis untuk kalian, tanpa peringatan dari komponen bersarang.

Secara keseluruhan hal ini menghapus kebutuhan untuk semua kelas optimisasi performa dari piring pengembang, dan membuat mereka dapat lebih dokus dalam mengembangkan aplikasi itu sendiri secara berskala.

### HTML & CSS

Di React, semuanya hanyalah JavaScript. Tidak hanya struktur HTML diekspresikan melalui JSX, trend terkini juga lebih mengarah untuk meletakkan manajemen CSS didalam JavaScript juga. Pendekatan ini memiliki kelebihannya tersendiri, tetapi juga datang dengan beragam pertukaran yang mungkin terlihat tidak berdampak untuk semua developer.

Vue menganut teknologi web klasik dan mengembangkan diatas mereka. Untuk menunjukan kalian maksud tersebut, kita akan terjun ke beberapa contoh.

#### JSX vs Templat-templat

Di React, semua komponen menampilkan antarmuka mereka didalam fungsi penyaji menggunakan JSX, sebuah deklari seperti sintaks XML yang bekerja dengan JavaScript.

Fungsi penyaji dengan JSX memiliki beberapa keunggulan:

- Kalian bisa mengungkit kemampuan penuh bahasa pemrograman (JavaScript) untuk mengembangkan tampilan kalian. Hal ini termasuk variable sementara, kendali arus, dan mereferencsi nilai JavaScript secara langsung didalam lingkup

- Perlengkapan yang didukung (contohnya linting, pengecekan ketikan, pelengkapan otomatis editor) untuk JSX dalam beberapa cara lebih unggul dibanding apa yang ada saat ini untuk templat Vue.

Di Vue, kami juga memiliki [fungsi render](render-function.html) dan bahkan [dukungan JSX](render-function.html#JSX), karena terkadang kalian pasti membutuhkan keunggulan tersebut. Bagaimanapun juga, sebagai sebagai pengalaman awal kami menawarkan templat sebagai alternatif yang lebih sederhana. HTML berlaku manapun juga berlaku pada templat Vue, dan ini menuntun kepada beberapa keunggulannya tersendiri: 

- Untuk kebanyakan pengembang yang bekerja dengan HTML, templat lebih serasa alami untuk dibaca dan ditulis. Preferensi itu sendiri entah bagaimana dapat menjadi subjektif, tetapi jika hal tersebut membuat pengembang lebih produktif kemudian keunggulan tersebut adalah objektif.

- Templat yang berdasar HTML membuat templat tersebut sangat mudah untuk dimigrasikan ke aplikasi yang sudah ada untuk mendapatkan keuntungan dari fitur reaktifitas Vue.

- Hal tersebut juga membuat templat lebih mudah bagi designer dan pengembang yang kurang berpengalaman untuk menguraikan dan berkontribusi ke basis kode.

- You can even use pre-processors such as Pug (formerly known as Jade) to author your Vue templates.

Sebagian berargumen bahwa kalian butuh mempelajari bahasa DSL (Domain-Specific Language) ekstra untuk dapat menulis templat - kami percaya perbedaan ini sangat tidak dalam. Pertama, JSX bukan berarti pengguna tidak harus mempelajari apapun - hal ini adalah sintaks tambahan untuk JavaScript polosan, dengan begitu akan menjadi hal yang mudah untuk seseorang yang akrab dengan JavaScript untuk memepelajarinya, tetapi mengatakan jika itu dasarnya gratis sebenarnya menyesatkan. Sama halnya, sebuah templat juga sintaks tambahan untuk HTML polosan dan oleh sebab itu memiliki ganjaran yang sangat rendah bagi mereka yang sudah akrab dengan HTML. Dengan adanya DSL kami juga dapat membantu pengguna menyelesaikan banyak hal dengan kode yang sedikit (contoh pengubah `v-on`). Tugas yang sama dapat melibatkan lebih banyak kode ketika menggunakan JSX polosan atau fungsi penyaji.

Dibidang yang lebih tinggi, kida dapat membagi komponen menjadi dua kategori: presentasi dan logika. Kami merekomendasikan menggunakan templat untuk komponen presentasi dan fungsi penyaji / JSX untuk logika. Persentase dari kedua komponen bergantung pada tipe aplikasi yang kalian kembangkan, tapi secara umum kami lebih menemukan presentasi lebih sering digunakan.

#### Lingkup Komponen CSS

Kecuali kamu menyebarkan komponen ke beberapa files (contohnya didalam [modul CSS](https://github.com/gajus/react-css-modules)), lingkup CSS di React lebih sering diselesaikan dengan solusi CSS-dalam-JS (contohnya [styled-component](https://github.com/styled-components/styled-components), [glamorous](https://github.com/paypal/glamorous), dan [emotion](https://github.com/emotion-js/emotion)). Hal ini mengenalkan kepada sebuah paradigma tampilan orientasi berdasarkan komponen yang berbeda dengan proses penulisan CSS biasa. Sebagai tambahan, walaupun disana terdapat dukungan untuk mengekstrak CSS menjadi satu stylesheet saat waktu pembuatan, hal ini masih lazim untuk waktu runtime akan butuh dimasukan ke bundel agar tampilan bekerja dengan tepat. Sementara kalian mendapat akses kedinamisan JavaScript selagi mendirikan tampilan kalian, sisi lainnya biasanya akan meningkatkan ukuran bundel dan memakan waktu runtime.

Jika kamu adalah fans dari CSS-didalam-JS, banyak libary CSS-didalam-JS yang mendukung Vue (contohnya [styled-components-vue](https://github.com/styled-components/vue-styled-components) dan [vue-emotion](https://github.com/egoist/vue-emotion)). Perbedaan utama diantara React dan Vue disini adalah metode awal dari styling dalam Vue melalui keakraban tag `style` dalam [komponen satu-file](single-file-components.html).

[komponen satu-file](single-file-components.html)) memberikan kalian akses penuh ke CSS dengan file yang sama seperti sisa kode komponen kalian.

``` html
<style scoped>
  @media (min-width: 250px) {
    .list-container:hover {
      background: orange;
    }
  }
</style>
```

atribut tambahan `scoped` otomatis melingkup CSS tersebut kedalam komponen kalian dengan cara menambahkan atribut unik (seperti `data-v-21e5b78`) ke element dan menghimpun `.list-container:hover` menjadi seperti `.list-container[data-v-21e5b78]:hover`.

Yang terakhir, tampilan di komponen satu-halam Vue sangat fleksibel. Melalui [vue-loader](https://github.com/vuejs/vue-loader), kalian bisa menggunakan preprocessor, post-processor dan bahkan integrasi mendalam apa saja dengan [CSS Modules](https://vue-loader.vuejs.org/en/features/css-modules.html) -- semua berada didalam elemen `<style>`.

### Skala

#### Menaikan skala

Untuk aplikasi besar, Vue dan React keduanya menawarkan solusi rute robust. Komunitas react juga sudah menjadi sangat inovatif dalam istilah solusi manajemen keadaan (contohnya Flux/Redux). Pola manajemen keadaan tersebut dan [bahkan Redux itu sendiri](https://yarnpkg.com/en/packages?q=redux%20vue&p=1) dapat dengan mudah diintegrasikan ke aplikasi Vue. Bahkan, Vue sudah mendalami model ini selangkah didepan dengan [Vuex](https://github.com/vuejs/vuex), sebuah solusi manajemen keadaan terinspirasi dari Elm yang dapat diintegrasikan secara mendalam pada Vue yang kami pikir menawarkan keunggulan pengalaman mengembangkan sebuah aplikasi.

Perbedaan penting lainnya antara penawaran ini adalah library pendukung library Vue untuk manajemen keadaan dan merute (diantara [kepentingan lain](https://github.com/vuejs)) semuanya sudah didukung secara resmi dan dijaga agar tetap mutakhir dengan library utama. Sedangkan React memilih untuk meninggalkan kepentingan kepada komunitasnya, membuat ekosistem yang berkeping keping. Walaupun menjadi lebih terkenal, ekosistem React bisa dianggap lebih kaya dibandingkan milik Vue.

Dan akhirnya, Vue menawarkan sebuah [CLI generator proyek](https://github.com/vuejs/vue-cli) yang membuat hal seperti membuat proyek baru sangatlah mudah dengan menggunakan sistem bangunan kalian, termasuk [webpack](https://github.com/vuejs-templates/webpack), [Browserify](https://github.com/vuejs-templates/browserify), atau bahkan [tanpa sistem bangunan](https://github.com/vuejs-templates/simple). React juga membuat aspek seperti ini dengan [create-react-app](https://github.com/facebookincubator/create-react-app), tetapi saat ini memiliki beberapa batasan: 

- Hal ini tidak memperbolehkan konfigurasi apapun selama pembangunan proyek, sementara templat proyek Vue memperbolehkan kustomisasi seperti [Yeoman](http://yeoman.io/).
- Hal ini hanya menawarkan sebuah templat yang mengasumsikan kalian membuat sebuah aplikasi satu-halaman, sementara Vue menawarkan berbagai macam templat untuk berbagai macam tujuan dan sistem banguna.
- Hal ini tidak dapat membangun templat proyek yang dibangun oleh pengguna, dimana bisa sangat berguna untuk lingkungan perusahaan dengan sistem yang sudah disediakan.

Hal ini menjadi sangat penting untuk dicatat bahwa terdapat banyak batasan desain yang terdapat disini adalah kesengajaan yang ditentukan dari tim create-react-app dan tentu saja menawarkan keunggulan mereka. Contohnya, selama kebutuhan proyek kamu sangat sederhana dan kamu tidak butuh untuk "cabut" untuk mengkustomisasi proses pembangunan kalian, kalian juga akan dapat memperbaruinya sebagai dependency. Kalian bisa baca lebih tentang [membedakan filosofi disini](https://github.com/facebookincubator/create-react-app#philosophy).

#### Menurunkan skala

React juga dikenal karena betapa terjal kesulitan pembelajarannya. Sebelum kalian bisa memulai, kalian harus tau tentang JSX dan mungkin ES2015%, karena banyak contoh yang menggunakan syntax kelas React. Kalian juga harus mempelajari tentang sistem bangunan, walaupun karena kalian secara teknik dapat menggunakan Babel sendiri untuk menghimpun secara langsung kode kalian di browser, hal ini sangat tidak cocok untuk produksi.

Sementara menaikan skala dalam Vue sama baiknya dengan React, menurunkan skala juga sama baiknya seperti jQuery. Itu benar, untuk memulai, semua yang kalian butuhkan hanya memasukan sebuah tag skrip kedalam halaman tersebut:

``` html
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

Kemudian kalian dapat memulai menulis kode Vue, dan bahkan meluncurkan versi minified ke produksi tanpa merasa bersalah atau harus cemas karena masalah performa.

Karena kalian tidak perlu tahu tentang JSX, ES2015, atau sistem bangunan untuk memulai Vue, Vue juga biasanya hanya memakan waktu pengembang kurang dari sehari untuk membaca [panduannya](./) untuk mempelajari hal yang cukup untuk membangun aplikasi yang tidak mengada-ngada.

### Penyajian Native

React Native membuat kalian dapat menulis aplikasi sajian-native untuk iOS dan Android menggunakan model komponen React yang sama. Hal ini bagus karena sebagai pengembang, kalian bisa mengaplikasikan pengetahuan kalian tentang framework ke berbagai platform. Pada waktu mendatang, Vue sudah memiliki kolaborasi resmi dengan [Weex](https://weex.apache.org/), sebuah framework antarmuka lintas-platform yang dibuat oleh grup Alibaba dan di inkubasi oleh Lembaga Perangkat Lunak Apache (dalam bahasa inggris Apache Software Foundation atau ASF). Weex membuat kalian dapat menggunakan sintaks komponen Vue yang sama untuk menulis komponen yang tidak hanya dapat disajikan di browser, tapi juga secara native pada iOS dan Android!

Pada saat ini, saat ini Weex masih aktif dikembangkan dan tidak sedewasa dan sudah teruji perang seperti React, tetapi perkembangannya didorong oleh kebutuhan produksi dari bisnis komersional elektronik (e-commerce) di dunia, dan tim Vue juga aktif berkolaborasi dengan tim Weex untuk memastikan pengalaman yang nyaman untuk para pengembang.

Pilihan laih adalah plugin [NativeScript-Vue](https://nativescript-vue.org/), a [NativeScript](https://www.nativescript.org/) untuk membangun aplikasi pekat dengan native menggunakan Vue.js.

### Dengan MobX

MobX menjadi sangat terkenal di komunitas React dan MobX sendiri sebenarnya menggunakan sistem reaktif yang hampir mirip dengan Vue. Dengan beberapa batasan, alur kerja React + MobX dapat Vue yang lebih bertele-tele, jadi jika kalian menggunakan kombinasi tersebut dan terasa menyenangkan, melanjutkan ke Vue mungkin adalah langkah masuk akal selanjutnya.

### Preact dan Library yang mirip dengan React lainnya

Library yang mirip dengan React biasanya mencoba untuk membagikan API dan ekosistem mereka sebanyak mungkin dengan react semudah mungkin. Dengan alasan tersebut, kebanyakan mayoritas pada perbandingan diatas juga berlaku ke mereka. Perbedaan utama biasanya adalah ekosistem yang berkurang, sering diantaranya, dibandingkan dengan React. Karena library ini tidak 100% cocok dengan segala ekosistem React, beberapa peralatan dan pasangan library tidak dapat digunakan. Atau, bahkan jika mereka terlihat bekera, mereka dapat rusak kapan saja kecuali library mirip React kalian didukung secara resmi setara dengan React.

## AngularJS (Angular 1)


Beberapa dari sintaks Vue akan terlihat sangat mirip dengan AngularJS (contohnya `v-if` vs `ng-if`). Hal ini karena banyak hal dari AngularJS bekerja dan sudah menjadi inspirasi dari Vue pada waktu pengembangan. Ternyata disana juga terdapat banyak jerit payah datang dengan AngularJS, dimana Vue memiliki percobaan untuk menawarkan perbaikan yang signifikan.

### Kompleksitas

Vue sangat sederhana dibanding AngularJS, keduanya pada aspek API dan desain. Pembelajaran yang cukup untuk membangun aplikasi yang tidak mengada-ngada biasanya memakan waktu kurang dari satu hari, yang mana tidak benar untuk AngularJS

### Flexibilitas dan Modularitas

AngularJS memiliki opini yang kuat tentang harus bagaimana aplikasi kalian terstruktur, sementara Vue lebih ke solusi flexibel, modular. Sementara hal ini membuat Vue lebih dapat diadaptasikan dengan berbagai macam proyek, kami juga mengenal bahwa memiliki beberapa pilihan yang ditentukan olehmu sesekali berguna, jadi kalian bisa memulai koding.

Itulah mengapa kami menawarkan [templat webpack](https://github.com/vuejs-templates/webpack) yang bisa membantu kalian menyiapkan dalam beberapa menit, sekaligus menunjang kalian untuk mengakses fitur lanjutan seperti pemuatan ulang modul dengat hangat, linting, mengekstrak CSS, dan masih banyak lagi

### Mengikat data

AngularJS menggunakan pengikat dua-arah diantara lingkupan, sementara Vue memperkuat sebuah alur data satu-arah diantara komponen. al ini membuat alur dari data menjadi lebih mudah untuk di aplikasi yang tidak mengada-ngada.

### Instruksi vs Komponen

Vue sudah menjelaskan berbedaan diantara Instruksi dan komponen. Instruksi berarti mengkapsulasi manipulasi DOM saja, sementara komponen adalah unit dalam diri sendiri yang memiliki tampilan dan logika data masing-masing. Didalam AngularJS, Instruksi melakukan apasaja dan komponen hanyalah salah satu dari tipe Instruksi.

### Performa runtime

Vue memiliki performa yang lebih baik dan lebih, lebih mudah untuk di optimasi karena tidak menggunakan pengecakan kasar. AngularJS menjadi lamban karena memiliki banyak pengawas, karena setiap kali apapun berupah didalam lingkupan, semua pengawas ini harus mengevaluasi ulang lagi. Dan juga, rotasi penggalian dapat juga dijalankan berulang kali untuk "menstabilkan" jika beberapa pengawas memicu pembaruan lain. Pengguna AngularJS harus sering kembali ke teknik batin untuk melewati rotasi penggalian ini, dan di beberapa situasi, tidak terdapat jalan untuk mengoptimasi sebuah lingkupan dengan banyak pengawas.

Vue tidak terbebani dengan hal ini sama sekali karena Vue menggunakan sistem observasi pelacakan dependency transparan dengan pengantrian async - semua perubahan dipicu secara terpisah kecuali mereka memiliki hubungan dependency yang tertera.

Yang menariknya, disana terdapat beberapa kemiripan pada bagaimana Angular dan Vue dalam menghadapi masalah AngularJS.

## Angular (Dikenal juga dengan Angular 2)

Kami memiliki bagian terpisah untuk Angular terbaru karena Angular baru tersebut adalah framework yang berbeda todal dari AngularJS. Contohnya, Angular mengutamakan fitur sistme komponen kelas-pertama, banyak detil implementasi yang sudah ditulis ulang, dan API juga diubah secara drastis.

### TypeScript

Angular mewajibkan menggunakan TypeScript, diketahui dengan semua dokumentasinya dan sumber pembelajarannya berdasarkan dari TypeScript. TypeScript memiliki bebera keuntungannya - tipe pengecekan statis bisa sangat berguna untuk aplikasi skala-besar, dan bisa menjadi dorongan produktifitas yang cukup besar bagi pengembang dengan latar belakang Java dan C#.

Bagaimanapun Juga, tidak semua orang ingin menggunakan TypeScript. Dalam banyak kasus berskala lebih kecil, mengenalkan sebuah typesystem bisa menambah beban daripada meningkatkan produktifitas. Dalam kasus tersebut, kalian lebih baik menggunakan Vue, karena menggunakan Angular tanpa TypeScript dapat menjadi sangat menantang. 

Dan yang terakhir, walaupun tidak terintegrasi secara dalam dengan TypeScript seperti Angular, Vue juga menawarkan [typings resmi](https://github.com/vuejs/vue/tree/dev/types) dan [decorator resmi](https://github.com/vuejs/vue-class-component) untuk mereka yang ingin menggunakan TypeScript dengan Vue. Kami juga secara aktif berkolaborasi dengan TypeScript dan tim VSCode di Microsoft untuk meningkatkan pengalaman TS/IDE untuk pengguna Vue + TS.

### Performa runtime

Kedua framework terbilang cepat, dengan kesamaan pengukuran pada beberapa tolak ukur. Kalian bisa [menjelajah pengukuran spesifik](https://stefankrause.net/js-frameworks-benchmark8/table.html) untuk perbandingan yang lebih lengkap, tapi kecepatan sepertinya tidak akan menjadi faktor penentu.

### Ukuran

Versi Angular terbaru, dengan [kompilasi AOT](https://en.wikipedia.org/wiki/Ahead-of-time_compilation) dan [tree-shaking](https://en.wikipedia.org/wiki/Tree_shaking), sudah sangat dapat menurunkan ukurannya. Tetapi, sebuah fitur-lengkap proyek Vue 2 termasuk Vuex + Vue Router (~30KB jika di gZip) masih sedikit lebih ringan daripada out versi mentah, aplikasi penghimpunan-AOT dibangun oleh `angular-cli` (~65KB jika di gZip).

### Flexibilitas

Vue lebih sedikit teropini dibanding Angular, menawarkan dukungan resmi untuk beragam sistem bangunan, tanpa ada batasan bagimana struktur aplikasi kalian. Banyak pengembang menikmati kebebasan ini, tetapi sebagian lebih memilih hanya memiliki satu cara untuk membangun aplikasi.

### Lengkung pembelajaran (Tingkat kesulitan pembelajaran)

Untuk mulai menggunakan Vue, semua yang kalian butuhkan hanyalah keakraban dengan HTML dan ES5 JavaScript (seperti JavaScript polosan). Dengan skill dasar tersebut, kalian bisa mulai membuat aplikasi sederhana dengan waktu kurang dari sehari hanya dengan membaca [panduan](./).

Lengkung pembelajaran Angular lebih terjal. Permukaan API dari framework sangat besar dan sebagai pengguna kalian butuh membiasakan diri kalian dengan banyak konsep sebelum semakin produktif. Kompleksitas dari Angular besar karena tujuan desainnya hanyalah aplikasi besar, dan komplex- tetapi hal tersebut tidak membuat framework tidak susah untuk dipelajari untuk pengembang yang kurang berpengalaman.

## Ember

Ember adalah framework berfitur-lengkap yang di desain menjadi berpendirian teguh. Ember penyediakan banyak konvensi yang mapan dan setelah kalian terbiasa dengannya, dapat membuat kalian sangat produktif. Tetapi, hal ini berarti lengkung pembelajaran menjadi tinggi dan fleksibilitas terbebani. Hal ini sebuah pertukaran ketika kalian mencoba untuk mengambil diantara framework yang berpendirian dan beberapa peralatan library yang longgar yang dapat bekerja sama. Yang terakhir memberikan kalian kebebasan lebi tetapi juga membutuhkan kalian untuk membuat keputusan arsitektur.

Dengan kata lain, hal ini akan menjadi perbandingan yang lebih baik diantara inti Core dan lapisan [templating](https://guides.emberjs.com/v2.10.0/templates/handlebars-basics/) dan [model objek](https://guides.emberjs.com/v2.10.0/object-model/) Ember: 

- Vue menyediakan reaktifitas yang tidak terpaku pada objek JavaScript polosan dan properti sepenuhnya dihitung secara otomatis. Di Ember, kalian harus membungkus semuanya didalam Objek Ember dan mendeklarasikan dependency untuk menghitung property.

- Sintak Templat Vue memanfaatkan kekuatan penuh ekspresi JavaScript, sementara ekspresi Handlebar dan sintaks penolong secara sengaja dibatasi pada perbandingan.

- Kebijakan performa, Vue melampaui Ember [dengan perbandingan yang tidak sedikit](https://stefankrause.net/js-frameworks-benchmark8/table.html), bahkan setelah pembaruan mesin Glimmer pada Ember 3.x. Vue memperbarui tumpukan secara otomatis, sementara di Ember kalian harus mengelola putaran penjalanan secara manual pada situasi krisis performa.

## Knockout

Knockout sebelumnya adalah pelopor dalam MVVM dan pelacak ruang dependency dan sistem reaktifitasnya sangat mirip dengan milik Vue. [Browser supportnya](http://knockoutjs.com/documentation/browser-support.html) juga yang sangat impressive mempertimbangkan semua yang dikerjakan, sampai dukungan hingga IE6! Vue pada sisi lain hanya mendukung hingga IE9+.

Seiring waktu, pengembangan Knockout telah melambat dan mulai menampakkan usianya sedikit demi sedikit. Contohnya sistem komponennya kurang satu set penuh kait lingkaran hidup dan walaupun sudah kasus yang umum, antarmuka untuk memindahkan turunan ke komponen masih terasa sedikit kikuk dibanding dengan [Vue's](components.html#Content-Distribution-with-Slots).

Disana sepertinya juga terdapat perbedaan filosofi pada desain API dimana jika kalian penasaran, dapat di demonstrasikan dengan bagaimana caranya setiap menangani pembuatan [todo list sederhana](https://gist.github.com/chrisvfritz/9e5f2d6826af00fcbace7be8f6dccb89). Hal ini entah bagaimana bersifat subjektif, tapi banyak yang mempertimbangkan API milik Vue lebih sedikit kompleks dan terstruktur.

## Polymer

Polymet adalah proyek yang disponsori Google lainnya dan sebenarnya adalah sumber inspirasi dari Vue juga. Komponen dari Vue bisa menjadi longgar dibandingkan element kustom dari Polymer dan keduanya menyediakan gaya pengembangan yang mirip. Perbandingan yang sangat besar adalah Polymer dibuat setelah fitur Komponen Web terbaru dan membutuhkan polyfills yang tidak sepele untuk bekerja (dengan penurunan performa) pada browser yang tidak mendukung fitur secara native. Secara kontras, Vue bekerja tanpa dependency atau polyfills apapun hingga IE9.

Di Polymer, timnya juga telah membuat sistem pengikat data yang sangat terbatas untuk menebus performanya. Contohnya, satu-satunya ekspresi yang didukung di templat Polymer adalah penyangkalan boolean dan pemanggilan method tunggal. Implementasi komputasi propertinya juga sangat tidak fleksibel.

## Riot

Riot 3.0 menyediakan model pengembangan berdasar komponen (yang mana dipanggil "tag" pada Riot), dengan API yang elok dan minimal. Riot dan Vue mungkin memiliki banyak kesamaan filosofi desain. Tetapi, meskipun menjadi lebih berat dari Riot, Vue juga menawarkan beberapa keunggulan:

- Performa yang lebih baik. Riot [melintasi sebuah pokok DOM](http://riotjs.com/compare/#virtual-dom-vs-expressions-binding) daripada menggunakan virtual DOM, yang membuatnya terbebani oleh masalah performa dengan AngularJS.
- Dukungan peralatan yang lebih matang. Vue menyediakan dukungan resmi untuk [webpack](https://github.com/vuejs/vue-loader) dan [Browserify](https://github.com/vuejs/vueify), sementara Riot bergantung pada dukungan komunitas untuk integrasi sistem bangunan.
