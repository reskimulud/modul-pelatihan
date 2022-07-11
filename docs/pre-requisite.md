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

![JavaScript](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/git.svg) &nbsp;
![JavaScript](https://raw.githubusercontent.com/reskimulud/reskimulud/main/logo-svg/github-icon.svg)

**[<< Sebelumnya](README.md)** | **[Selanjutnya >>](instalasi.md)**