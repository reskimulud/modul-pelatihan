# Mengakses file dari Server

Jika sebelumnya kita telah berhasil mengupload file gambar ke dalam server, namun sebetulnya file tersebut belum bisa di akses dari luar. Secara default, Hapi tidak mengizinkan client mengakses sebuah file, namun terdapat sebuah plugin dari Hapi jika kita ingin mengakses suatu file yang tersimpat di sebuah directory. Plugin yang dimaksud adalah **Inert**. 

Tidak perlu berlama-lama, mari kita install dependency tersebut dengan menjalankan perintah berikut 

```bash
npm install @hapi/inert
```

Setelah inert berhasil diinstall, sekarang bukalah file `server.js` dan import plugin tersebut ke dalam server

```js
....

const Inert = require('@hapi/inert');
```

Masih di dalam file `server.js`, tambahkanlah inert ke dalam plugin external (setelah memasangkan plugin jwt) dengan menambahkan kode berikut

```js
...

const init = async () => {
  ....

  // register external plugin
  await server.register([
    ...
    {
      plugin: Inert, // tambahkan plugin ini
    },
    ...
  ]);

  ....
};
```

Sekarang kita buat metode untuk mengakses file gambar produk yang mengarah tempat penyimpanan gambar tersebut (`src/api/products/images`) di dalam folder `routes.js` pada fitur **Products**. Maka bukalah file `routes.js`, tambahkan method baru di bawahnya dengan mengikuti kode berikut

```js
const path = require('path'); // import path untuk mengakses folder tempat menyimpan file gambar produk

const routes = (handler) => [
  ...

  {
    method: 'GET',
    path: '/products/image{param*}',
    handler: {
      directory: {
        path: path.resolve(__dirname, 'images'),
      },
    },
  },
];
```

Mantap. Sekarang silahkan buka postman dan coba untuk mengambil gambar dengan mengetikan url seperti berikut `http://localhost:5000/products/image/<nama_file>`. Jika berhasil mengambil gambar artinya sudah sukses.

Simpan perubahan ke dalam Git dan push ke GitHub dengan perintah berikut

```bash
git add .
git commit -m "menambahkan metode untuk mengakses file gambar dari server"
git push
```

**[<< Sebelumnya](m13-upload.md)** | **[Selanjutnya >>]()**