# Tokenisasi

Kita telah selesai membuat fitur login dan register serta telah membuat fitur products, namun masih ada kendala khususnya di keamanan. Dalam fitur menambah atau mengubah data produk, siapapun dapat mengambil dan mengubah data **bahkan belum login** sekalipun. Ini sangat berbahaya jika dibiarkan. Untuk mengamankannya, kita akan membuat metode **tokenisasi**, dimana ketika client ingin mengubah isi data atau menambahkan data baru harus ter-authorisasi dengan cara login terlebih dahulu, dan di setiap request nya harus **disertai dengan token**.

Untuk membuat token kita akan membutuhkan sebuah library yang namanya **Json Web Token** ([JWT](htps://jwt.io)). Sesuai namanya, JWT menggunakan string JSON sebagai datanya, setelah itu akan di encode menjadi **bash64**. Untuk membuat JWT kita memerlukan sebuah **secret key**. Semakin rumit secret key nya, maka akan semakin aman pula token JWT nya.

Tidak perlu berlama-lama, mari kita implementasikan JWT pada aplikasi kita!

# Install JWT di NPM

Sebelum memulai, pastikan terlebih dahulu kita telah meninstall JWT. Framework Hapi telah menyediakan sebuah **plugin eksternal** untuk menggunakan JWT. Langsung saja kita install dengan menjalankan perintah berikut

```bash
npm install @hapi/jwt
```

# Membuat Secret Key

Sekarang kita akan membuat sebuah secret key. Buka file `.env` dan buat variable berikut

```.env
TOKEN_KEY=71aa13d1119702e7437790f140d7c3493f5d0234bad9fc388f4bfc38a218044dd39cbdb9cfa62a4dfb2959cb52822b43d1c8370a868c11a6eb629ec68d975b74
```

Key di atas akan kita gunakan sebagai secret key saat akan membuat token JWT.

# Register Eksternal Plugin dan Authentication Strategy

Setelah itu kita kita harus mendaftarkan/register plugin JWT ke dalam server dengan cara ikuti kode berikut

```js
...
const Jwt = require('@hapi/jwt');
...

const init = () => {
  ....

  // register external plugin
  await server.register([
    {
      plugin: Jwt,
    },
  ]);

  // internal plugin
  ...
}
```

Setelah selesai memasangkan plugin JWT, saatnya kita untuk membuat strategi untuk memvalidasi token yang user/client kirimkan, dan dengan strategi ini akan otomatis dibuatkan response gagal/error jika token tidak valid. Berikut kode nya

```js
const init = () => {
  ...

  // defines authentication strategy
  server.auth.strategy('eshop_jwt', 'jwt',{
    keys: process.env.TOKEN_KEY,
    verify: {
      aud: false,
      iss: false,
      sub: false,
    },
    validate: (artifacts) => ({
      isValid: true,
      credentials: {
        id: artifacts.decoded.payload.id,
      },
    }),
  });

  ...
}
```

> Perhatikan di bagian credentials, kita bisa mengambil ID user dengan memanggil `request.auth.credentials` di handler, ini yang akan kita gunakan untuk authorization. Kita akan bahas pada modul berikutnya.

Parameter pertama pada method **strategy()** adalah nama dari strategi (`eshop_jwt`), dimana nama ini akan kita gunakan saat menentukan method yang mengharuskan melampirkan token saat request. Dan parameter kedua adalah nama plugin yang memverifikasi token, yaitu `jwt`.

# Menambahkan Token ke Method di Routes

Sekarang kita akan menentukan method mana yang mengharuskan user/client melampirkan token pada saat mengirimkan request. Untuk saat ini kita akan menggunakannya pada fitur produk yaitu di method `POST`, `PUT` dan `DELETE` (membuat produk baru, mengubah dan menghapus). Untuk itu kita buka file `routes.js` pada folder `products` dan tambahkan options berikut

```js
const routes = (handler) => [
  {
    method: 'POST',
    path: '/products',
    handler: handler.postProduct,
    options: {
      auth: 'eshop_jwt',
    },
  },
  {
    method: 'PUT',
    path: '/products/{id}',
    handler: handler.putProductById,
    options: {
      auth: 'eshop_jwt',
    },
  },
  {
    method: 'DELETE',
    path: '/products/{id}',
    handler: handler.deleteProductById,
    options: {
      auth: 'eshop_jwt',
    },
  },
];
```

> Method GET tidak kita tambahkan tokenisasi karena setiap orang dapat melihat daftar produk bahkan sebelum login

# Meng Generate Token saat Login

Kita akan membuat atau meng geneate token JWT pada saat user login, kita akan membuat method generate token di file `handler.js` atau **AuthenticationHandler**. Untuk itu bukalah file nya dan ikuti langkah berikut

```js
const Jwt = require('@hapi/jwt');

class AuthenticationHandler {
  ....

  #generateToken(payload) {
    return Jwt.token.generate(payload, process.env.TOKEN_KEY)
  }

  ...

  async postLogin(request, h) {
    ...

    const payloadToken = { id, email, role }; // data yang ada di token
    const token = this.#generateToken(payloadToken);  // meng generate token

    return {
      status: 'success',
      message: 'User berhasil login',
      data: {
        id,
        token, // mengembalikan response
      },
    };
  }

  ....
}
```

Mantaps, kita telah selesai mengimplementasikan tokenisasi pada aplikasi kita. Saatnya untuk mencobanya di Postman. Cobalah untuk menambahkan data produk tanpa menggunakan token dan setelah itu login kembali dan masukan token yang sudah dibuatkan.

Simpan perubahan ke dalam Git dan push ke GitHub dengan perintah berikut

```bash
git add .
git commit -m "membuat fitur tokenisasi"
git push
```

**[<< Sebelumnya](m8-products.md)** | **[Selanjutnya >>](m10-authorization.md)**