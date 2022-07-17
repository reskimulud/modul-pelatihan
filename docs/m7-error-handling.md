# Error Handling

Jika kita lihat kodingan kita sebelumnya, saat mengalami error (gagal menambahkan user karena email sudah digunakan, gagal validasi payload/body) kita hanya membuat exception dengan meng **instance** ke object/class **Error** saja, dan jika kia mencoba memberikan request yang gagal akan mengembalikan kode **500 Internal Server Error**. Tentu kita tidak ingin itu terjadi bukan, karena kita harus memberikan pesan yang jelas error apa yang sebenarnya terjadi.

Untuk itu kita akan membuat **Custom Exception** yang akan menangani error  dan akan memberikan **status code** yang sesuai dengan apa yang terjadi. Tak usah berlama-lama, yuk kita buat folder `exceptions` di dalam folder `src` dan ikuti petunjuk selanjutnya.

# ClientError

Class Exception ini akan menjadi **parent/super class** bagi class exception yang lain. Class ini akan inherit/mewarisi exception **Error** dan akan membuat status code dengan nilai awal yaitu **400**. Berikut kode `ClientError.js` nya

```js
class ClientError extends Error {
  constructor(message, statusCode = 400) {
    super(message);
    this.statusCode = statusCode;
    this.name = 'ClientError';
  }
}

module.exports = ClientError;
```

# InvariantError

Sebenarnya kita tidak akan mengimplementasikan **ClientError** secara langsung, ClientError hanya sebagai **abstraksinya** saja, dan yang akan kita gunakan jika terjadi client error (400) yaitu **InvarianError**. Buat file nya dan ikuti seperti kode berikut

```js
const ClientError = require('./ClientError');

class InvariantError extends ClientError {
  constructor(message) {
    super(message);
    this.name = 'InvariantError';
  }
}

module.exports = InvariantError;
```

# NotFoundError

Selanjutnya kita akan membuat exception yang akan menangani error saat user meminta data yang tidak ada (**NotFound**). Buatlah file `NotFoundError.js` di dalam folder exceptions ,file ini akan inherit ke **ClientError** dan ubah status code nya menjadi **404**.

```js
const ClientError = require('./ClientError');

class NotFoundError extends ClientError {
  constructor(message) {
    super(message, 404);
    this.name = 'NotFoundError';
  }
}

module.exports = NotFoundError;
```

# AuthenticationError

Selanjutnya buat file `AuthenticationError.js` yang akan kita gunakan jika terjadi kegagalan saat login karena salah email atau password, dan ikuti kode nya seperti ini

```js
const ClientError = require('./ClientError');

class AuthenticationError extends ClientError {
  constructor(message) {
    super(message, 401);
    this.name = 'AuthenticationError';
  }
}

module.exports = AuthenticationError;
```

# AuthorizationError

Terakhir kita akan membuat error karena user melakukan hal yang diluar dari hak nya, seperti user yang menambahkan produk yang hanya bisa dilakukan oleh admin. Ini akan kita implementasikan saat kita membuat fitur **Authorization**. Buat file `AuthorizationError.js` dan ikuti kode nya seperti ini.

```js
const ClientError = require('./ClientError');

class AuthorizationError extends ClientError {
  constructor(message) {
    super(message, 403);
    this.name = 'AuthorizationError';
  }
}

module.exports = AuthorizationError;
```

Mantap, semuanya sudah siap kita implementasikan di kode kita sebelumnya

# Implementasi Error Handling

Pertama buka file `AuthenticationService.js` dan ubah Error  menjadi seperti ini

```js
class AuthenticationService() {
  ...

  async #verifyUserEmail(email) {
    ...

    if (result.length > 0 || result.affectedRows > 0) {
      throw new InvariantError('Gagal menambahkan user, email telah digunakan');
    }
  }
  ...
}
```
> Ubah Error menjadi **InvariantError** di method **verifyUserEmail()**, jangan lupa di import yah


```js
class AuthenticationService() {
  ...

  async register(email, name, password) {
    ...

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new InvariantError('Gagal menambahkan user');
    }

    return id;
  }

  ...
}
```
> Ubah Error menjadi **InvariantError** di method **register()** saat gagal menambahkan user

```js
class AuthenticationService {
  ...

  async login(email, password) {
    ...

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new AuthenticationError('Email atau password salah');
    }

    ...

    if (!isValid) {
      throw new AuthenticationError('Email atau password salah');
    }

    ...
  }

  ...
}
```
> Ubah Error menjadi **AuthenticationError** di method **login()** pada saat email atau password salah

```js
class AuthenticationService {
  ...

  async getUserById(userId) {
    ...

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new NotFoundError('User tidak ditemukan');
    }

    ...
  }

  ...
}
```
> Ubah Error menjadi **NotFoundError** di method **getUser()** pada saat user tidak ditemukan

Kita berpindah ke validator, tepatnya di file `index.js`

```js
...
throw new InvariantError(validationResult.error.message);
..
```
> Ubah semua Error menjadi **InvariantError** pada saat validasinya gagal/error

Langkah terakhir yaitu kita akan menangkap (**catch**) error-error tersebut di server dan mengembalikan response sesuai dengan pesan error dan code nya. Buka file `server.js` dan tambahkan kode berikut (sebelum `server.start()`)

```js
const init = () => {
  ...

  // extension
  server.ext('onPreResponse', (request, h) => {
    const {response} = request;

    if (response instanceof ClientError) {
      const newResponse = h.response({
        status: 'fail',
        message: response.message,
      });
      newResponse.code(response.statusCode);
      return newResponse;
    }

    console.log(response);

    return h.continue;
  });

  ...
}
```

Kode tersebut akan menangkap jika `response` error atau **instance** dari ClientError, maka akan dikembalikan response error beserta kode nya.

---

Mantap, kita telah mengimplementasikan Error Handling menggunakan custom exception. Saatnya untuk mencoba kembali di Postman dan coba untuk mengirim request gagal seperti register menggunakan email yang sudah terdaftar.

Jangan lupa simpan perubahan ke Git dan push ke GitHub

```bash
git add .
git commit -m "error handling dengan custom exceptions"
```

**[<< Sebelumnya](m6-auth.md)** | **[Selanjutnya >>](m8-products.md)**