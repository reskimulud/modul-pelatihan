# Sebelum Memulai

Sebelum mengikuti palatihan ini diharapkan kalian sudah menguasai kemampuan dasar dalam pemrograman khususnya `JavaScript` karena kita akan menggunakan bahasa pemrograman tersebut selama pelatihan. Selain JavaScript, diharapkan kalian juga telah menguasai atau setidaknya mengetahui dasar-dasar penggunaan `library` atau `tools-tools` berikut.

> Namun tenang, jika kalian belum menguasainya kita akan **belajar bersama-sama** untuk memmahami penggunaan tools tersebut.

# JavaScript

![JavaScript](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/javascript.svg)

JavaScript adalah bahasa pemrograman tingkat tinggi yang pada awalnya dikembangkan untuk membuat sebuah website. Bahasa ini mampu memberikan *logic* ke dalam website, sehingga website tersebut memiliki fungsionalitas tambahan dan lebih interaktif.

JavaScript termasuk ke dalam kategori scripting language. Apa maksudnya? Salah satu ciri-ciri utama dari bahasa scripting adalah kode tidak perlu dikompilasi agar bisa dijalankan. Scripting language menggunakan interpreter untuk menerjemahkan kode atau perintah yang kita tulis supaya dimengerti oleh mesin.

```js
console.log('Hello World!');
```

### Kenapa kita harus belajar JavaScript?

  * JavaScript dibuat dengan tujuan awal agar website menjadi lebih interaktif.
  * JavaScript termasuk ke dalam kategori scripting language, sehingga kode tidak perlu dikompilasi untuk bisa dijalankan. 
  * Terdapat interpreter untuk menerjemahkan kode kita agar bisa dimengerti oleh mesin.
  * Terdapat dua lingkungan umum untuk menjalankan JavaScript, yaitu browser dan Node.js
  * JavaScript dikembangkan dengan standar ECMAScript. Update besar terakhir tersaji dalam versi ES6 pada tahun 2015. Sejak saat itu, tiap tahun JavaScript melakukan update bersifat minor.

Karena pelatihan ini sepenuhnya akan menggunakan bahasa pemrograman `JavaScript` maka dibutuhkan kemampuan dasar untuk mengembangkan aplikasi menggunakan JavaScript seperti mendeklarasikan `variable`, mengetahui penggunaan `tipe data` membuat `function` dan sebagainya.

### Sintaks Dasar

```js
/**
 * Mendeklarasikan variable menggunakan sintaks 'const'
 * artinya variable tersebut hanya bisa diisi dengan nilai yang pertama
 * dan tidak bisa diinisialisasi ulang
 **/
const name = 'Reski Mulud Muchamad'; // pendeklarasian dan inisialisasi nilai variable bertipe data String
const age = 21; // variable bertipe data Number
const isLoveManCity = true; // variable bertipe data Boolean

function sayHello() { // pendeklarasian function
  let isLove; // pendeklarasian 'mutable' variable (bisa diinisialisasi ulang)

  if (isLoveManCity) { // pengkondisian
    isLove = 'saya menyukai club Manchester City';
  } else {
    isLove = 'saya tidak menyikai club Mancheste City';
  }

  // pengembalian nilai sebuah 'function' jika dipanggil
  return `Hallo, saya ${name}! usia saya ${age} tahun, dan ${isLove}`;
}

// memanggil function sayHello() dan mencetaknya di console
console.log(sayHello());
// output : Hallo, saya Reski Mulud Muchamad! usia saya 21 tahun, dan saya menyukai club Manchester City
```

### Materi Pendukung
  * [JavaScript MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
  * [An Introduction to JavaScript](https://javascript.info/intro)
  * Playlist Belajar JavaScript (Web Programming UNPAS)
    <iframe width="560" height="315" src="https://www.youtube.com/embed/videoseries?list=PLFIM0718LjIWXagluzROrA-iBY9eeUt4w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

# Node.JS

![Node.JS](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/nodejs.svg)

Node.js adalah **JavaScript Runtime** yang dapat mengeksekusi kode JavaScript di luar browser. Node.js seolah-olah menjadi gerbang bagi para JavaScript Developer untuk mengembangkan sistem di luar dari browser. JavaScript menjadi bahasa multiplatform yang banyak menggiring developer untuk menggunakannya. Popularitas JavaScript pun meroket! Pada tahun 2014 hingga 2020 JavaScript menjadi bahasa pemrograman nomor satu yang banyak digunakan oleh developer. Saat ini, JavaScript menjadi salah satu pilihan tepat dalam membangun web server, terlebih bila Anda adalah seorang Front-End Web Developer. Anda tentu tidak perlu menggunakan bahasa yang berbeda dalam membangun Back-End. Anda bisa menjadi Full-Stack Developer dengan mempelajari satu bahasa pemrograman saja dengan menggunakan Node.JS.

Untuk membuat sebuah proyek Node.JS, silahkan buka terminal dan jalankan kode berikut. Maka sebuah file bernama `package.json` secara otomatis akan dibuat.

```bash
npm init
```

NPM alias Node Package Manager merupakan JavaScript Package Manager bawaan dari Node.js. Melalui NPM ini kita dapat membuat Node.js package (proyek) dan mengelola penggunaan package eksternal yang digunakan. Leih detail nya akan kita bahas nanti

> Jika belum menginstall node sebelumnya, tutorial penginstallan node akan dijelaskan di modul berikutnya

### Materi Pendukung
  * [Documentation | Node.JS](https://nodejs.org/en/docs/)
  * [Introduction to Node.JS](https://nodejs.dev/learn/introduction-to-nodejs)
  * [Differences between Node.js and the Browser](https://nodejs.dev/learn/differences-between-nodejs-and-the-browser)
  * Playlist Belajar Node.JS (Web Programming UNPAS)
    <iframe width="560" height="315" src="https://www.youtube.com/embed/videoseries?list=PLFIM0718LjIW-XBdVOerYgKegBtD6rSfD" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

# Git dan GitHub

![GitHub](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/github-icon.svg)

Git adalah perangkat lunak yang disediakan secara open source (kode sumber terbuka). Git bertujuan untuk mendukung kolaborasi antar anggota tim serta mengontrol setiap versi perubahan dalam sebuah pekerjaan pengembangan aplikasi ataupun pada bidang lainnya. Git pertama kali dibangun oleh pengembang kernel Linux, yaitu Linus Torvalds pada tahun 2005. 

Dengan memanfaatkan Git, kita bisa berkolaborasi dalam tim. Jadi, kita tidak perlu lagi mengerjakan pengembangan sistem ataupun hal lainnya yang berbasis text dengan cara antrean alias menunggu anggota tim pertama selesai mengerjakan kemudian mengirimkan hasil pekerjaan kepada anggota berikutnya untuk melanjutkannya. Tentunya itu akan banyak memakan waktu dan juga tidak efektif. 

Pada implementasinya, saat menggunakan Git, kita akan dihadapkan dengan berbagai pengaturan awal yang diperlukan untuk membangun sebuah repository Git server sebagai ruang untuk menyimpan proyek yang akan dibangun.

Sedangkat GitHub adalah perusahaan yang menawarkan layanan hosting repository Git berbasis cloud. Pada dasarnya, GitHub membuat para pengguna individu dan tim menjadi lebih mudah untuk menggunakan Git dalam mengendalikan setiap versi pekerjaan saat melakukan kolaborasi di dalam ataupun antar tim. GitHub bisa kita akses di website nya : [github.com](https://github.com)

> Untuk panduan menginstall Git di komputer masing-masing dan panduan membuat akun GitHub akan dijelaskan di modul berikutnya.

### Perintah Git Yang Sering Digunakan

Karena kita akan memanfaatkan Git dan GitHub untuk mengembangkan aplikasi di pelatihan ini, jadi diharapkan kalian memahami alur untuk menggunakan Git, khususnya menggunakan *Comand Line Interface* (CLI). Meskipun terdapat tools yang memudahkan pengelolaan repository dengan aplikasi yang *GUI*, namun pengetahuan dasar akan perintah Git sangat diperlukan.

Untuk menjalankan perintah `git` kita bisa menggunakan *Comand Prompt* (CMD) atau *Powershell* di Windows, atau bisa menggunakan terminal bagi pengguna sistem operasi berbasis UNIX (Linux atau Mac OS). Selain itu, didalam paket instalasi git disediakan `git bash` yang bisa kita gunakan untuk menjalankan perintah git.

  1. Membuat Repository Baru

Repository adalah direktory atau folder tempat menyimpan projek dari aplikasi kitam, dimana nantinya Git akan merekam dan menyimpan perubahan dari kode yang kita buat. Untuk membuatnya kita cukup menjalankan perintah berikut

```bash
git init
```

  2. Mengetahui Status Repository

Untuk mengetahui status di repository, contohnya untuk mengetahui perubahan terjadi di file apa saja atau mengetahui apakah respository yang ada di local sudah sesuai dengan yang ada di remote atau belum. Cukup jalani perintah dibawah

```bash
git status
```

  3. Menambahkan File kedalam Staging Area

Untuk menambahkan file yang telah dimodifikasi atau baru dibuat kedalam Staging Area cukup jalani perintah berikut :

```bash
git add <nama_file> # jika ingin menambahkan file satu persatu
git add . # menambahkan semua file sekaligus yang ada di repo
```

  4. Merekam perubahan yang kita lakukan (commit)

Setelah selesai melakukan perubahan pad aplikasi, langkah selanjutnya yaitu merekam/commit perubahan tersebut. Untuk meng-commit kita bisa jalankan perintah berikut :

```bash
git commit -m "Menambahkan Fitur Baru (Login)"
```
> flag `-m` artinya kita akan menambahkan pesan commit dan masukan pesan/message didalam tanda `"`. Pesan commit ini sangat penting, karena supaya kita bisa tau perubahan apa yang telah kita lakukan.

Perintah git lainnya akan kita pelajari **pada saat materi berlangsung** sesuai dengan studi kasus yang tepat agar kita lebih memahami fungsi dari setiap perintah.

### Materi Pendukung
<iframe width="560" height="315" src="https://www.youtube.com/embed/videoseries?list=PLFIM0718LjIVknj6sgsSceMqlq242-jNf" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

# MySql

![MySQL](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/mysql.svg)

MySQL adalah sebuah DBMS (Database Management System) menggunakan perintah SQL (Structured Query Language) yang banyak digunakan saat ini dalam pembuatan aplikasi berbasis website. MySQL dibagi menjadi dua lisensi, pertama adalah Free Software dimana perangkat lunak dapat diakses oleh siapa saja. Dan kedua adalah Shareware dimana perangkat lunak berpemilik memiliki batasan dalam penggunaannya. 

MySQL termasuk ke dalam RDBMS (Relational Database Management System). Sehingga, menggunakan tabel, kolom, baris, di dalam struktur database -nya. Jadi, dalam proses pengambilan data menggunakan metode relational database. Dan juga menjadi penghubung antara perangkat lunak dan database server.

Secara garis besar, fungsi dari MySQL adalah untuk membuat dan mengelola database pada sisi server yang memuat berbagai informasi dengan menggunakan bahasa SQL. Fungsi lain yang dimiliki adalah memudahkan pengguna dalam mengakses data berisi informasi dalam bentuk String (teks), yang dapat diakses secara personal maupun publik dalam web.

Pembelajaran di pelatihan ini akan menggunakan MySQL sebagai database nya, pengtahuan dasar akan query/perintah SQL dibutuhkan, diharapkan sebelumnya sudah pernah menggunakan database MySQL seperti di mata kuliah Pemrograman Web I dan II.

### Struktur Query SQL

Berikut contoh query untuk operasi CRUD (Create Read Update Delete)

> Mengambil data
```sql
SELECT * FROM products LIMIT 10 ORDER BY date_added ASC;
```

> Memasukan data
```sql
INSERT INTO category_product (category_id, category_name, product_id)
VALUES (1, 'Fashion', 3);
```

> Memperbarui data
```sql
UPDATE products SET stock = 100 WHERE id = 2;
```

> Menghapus data
```sql
DELETE FROM products WHERE id = 3;
```

### Materi Pendukung
<iframe width="560" height="315" src="https://www.youtube.com/embed/fxe6qev-bno" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

# Postman

![Postman](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/postman.svg)

Postman merupakan tools yang sangat cocok untuk menguji sebuah API karena memiliki fungsi yang relatif lengkap sebagai API caller dalam melakukan HTTP Request. Bahkan untuk pengembangan API yang sudah kompleks, pengujian Postman dapat diintegrasikan ke dalam alur CI/CD.

Postman merupakan tools yang sangat powerful dan mudah untuk digunakan. Ia memiliki graphical user interface (GUI) sehingga dapat digunakan dan dipahami oleh kalangan pemula sekalipun. Sebab, kita tak lagi berhadapan dengan kode yang rumit hanya untuk membuat HTTP request seperti cURL.

Postman tersedia secara gratis dan dapat berjalan pada sistem operasi Windows, Linux, maupun macOS.

> Untuk instalasi Postman akan dijelaskan di modul berikutnya. Dan untuk mengetahui penggunaannya akan kita pelajari bersama-sama pada saat materi di kelas.

---

# Hapi.JS

![Hapi](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/hapi.svg)

Hapi.js merupakan framework untuk Node.js yang digunakan untuk pengembangan aplikasi yang membutuhkan application programming interface (API). Framework ini diciptakan oleh developer yang berkerja di Wallmart untuk menghadapi traffic yang padat saat Black Friday Online (sebuah hari dimana pelanggan bisa mendapatkan diskon super besar, di Indonesia terdapat event serupa dengan nama Hari Belanja Online Nasional).

Pada pelatihan ini kita akan mengunakan framework/library [Hapi.JS](https://hapi.dev) untuk mengembangkan API di sisi Backend nya, dan penggunaan nya akan dijelaskan di materi nanti. Jika ingin mengenal terlebih dahulu apa itu framework Hapi bisa kunjungi dokumentasi resmi nya.

  * [Website Resmi](https://hapi.dev)
  * [Dokumentasi](https://hapi.dev/tutorials/?lang=en_US)

# React.JS

![React](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/react.svg)

React adalah JavaScript library yang digunakan untuk membangun User Interface (antarmuka pengguna). Hal ini ditegaskan oleh tim pengembang React pada website resminya di reactjs.org. Dengan React, kita dapat terhindar dari banyak kesulitan yang biasa terjadi ketika menggunakan standar W3C dalam membangun antarmuka pengguna. Dilansir dari website resminya, React memanfaatkan konsep komponen, deklaratif, dan unidirectional data flow (aliran data searah). Berikut penjelasan singkatnya.

  * **Komponen** :
React memanfaatkan komponen dalam membangun antarmuka. Setiap komponen terenkapsulasi dan dapat saling dikomposisikan satu sama lain. Karena adanya komponen, antarmuka yang dibangun menggunakan React sangat reusable. Anda tidak perlu menuliskan kode yang sama berulang kali untuk menggunakan antarmuka yang serupa.


  * **Deklaratif** :
Dengan konsep deklaratif, pembuatan antarmuka pengguna dapat lebih cepat. Pasalnya, kita cukup fokus terhadap apa yang ingin dicapai. Tak ada kode imperatif lagi ketika menggunakan React. Bahkan, Anda bisa menuliskan “layaknya” sintaksis HTML di dalam kode JavaScript. Hal yang mustahil dilakukan oleh JavaScript standar saat ini. Karena itu, Anda bisa mengucapkan selamat tinggal pada fungsi DOM manipulation, seperti appendChild, getElementById, addEventListener, dan sebagainya.


  * **Aliran Data Searah** :
Komponen React dapat menampung sebuah data. React secara reaktif akan memperbarui dan me-render komponen jika data di dalamnya berubah. Karena sifat reaktifnya tersebut, kami rasa inilah alasan mengapa dinamakan React. Komponen React dapat dikomposisikan dan aliran data pada komponen dilakukan secara searah dari parent ke child. Hal itu membuat perubahan data pada React lebih terukur.

### Membuat project react

Untuk membuat project React, kita bisa memanfaatkan `create-react-app`. Berikut perintahnya :

```bash
npx create-react-app my-app # my-app adalah nama project nya, bisa diubah sesuai keinginan
cd my-app
code . # membuka project di Visual Studio Code
```

Di pelatihan ini kita akan menggunakan React.JS untuk membuat tampilan antarmuka di sisi Frontend. Ini akan sangat menyenangkan, maka dipersiapkan yah.

  * [Website Resmi](https://reactjs.org)
  * [Tutorial : Intro to React](https://reactjs.org/tutorial/tutorial.html)

---

# Heroku

![Heroku](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/heroku.svg)

Heroku adalah sebuah cloud platform atau tempat penyimpanan yang menjalakan bahasa pemrograman tertentu. Heroku juga termasuk ke dalam kriteria jenis Platform As A Service atau yang disingkat menjadi PaaS, yang dimana fungsi PaaS ini ialah user yang ingin melakukan penyebaran atau deploy aplikasi proyeknya ke heroku cukup dengan melakukan konfigurasi pada aplikasi proyek yang ingin Kamu deploy tentunya dan juga sudah terdapat platform untuk memungkinkan pengguna menjalankan, mengembangkan, dan bahkan mengelola aplikasi tanpa kompleksitas membangun serta memelihara infrastruktur data yang sudah terkait ke dalam pengembangan dan peluncuran aplikasi proyek.

Kita akan memanfaatkan heroku untuk mendeploy aplikasi kita khususnya untuk sisi Backend. Sebelum kita memulai menggunakan Heroku, alangkah baiknya kita membuat akun Heroku terlebih dahulu. Untuk panduan pembuatan akun heroku akan dijelaskan pada modul berikutnya.

---

# Netlify

![Netlify](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/netlify.svg)

Sama hal nya seperti Heroku, Netlify adalah sebuah platform yang menjadi sebuah tempat penyimpanan aplikasi atau projek. Namun bedanya Netlify biasanya digunakan untuk mendeploy aplikasi tampilan antar muka seperti React. Maka nanti kita akan menggunakan Netlify sebagai tempat untuk mendeploy aplikasi React kita.

Tutorial atau panduan pembuatan akun Netlify akan dibahas di modul berikutnya.

**[<< Sebelumnya](README.md)** | **[Selanjutnya >>](instalasi.md)**