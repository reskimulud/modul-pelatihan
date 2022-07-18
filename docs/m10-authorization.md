# Authorization

Sebelumnya kita telah mambuat tokenisasi, di dalam token yang kita buat terdapat `credential` yang memuat informasi user/client, data tersebut telah kita tambahkan kedalam request dan kita bisa memanfaatkan data tersebut sebagai credentials.

Saat ini yang akan kita implementasikan **authirization** adalah method untuk mengambil data user (**getUser()**). Sebelumnya client harus menyertakan params user id setiap kali ingin mengambil data user, hal ini harusnya dihindari, karena bisa saja client menggunakan id user lain. Maka dari itu kita akan terapkan **authorization** untuk method **getUser()**

# Authorize method getUser()

Silahkan buka file `handler.js` di dalam folder `authentication` dan hapus baris yang mengambil params `id` dan ubah menjadi baris berikut

```js
class AuthenticationHandler {
  ....

  async getUser(request, h) {
    const { id } = request.auth.credentials;
    const user = await this.#service.getUserById(id);

    ...
  }

  ....
}
```

Setelah itu buka file `routes.js` dan tambahkan option untuk menambahkan auth tokenisasi pada method `GET` user serta hapus **param** `{id}`

```js
const routes = (handler) => [
  ....

  {
    method: 'GET',
    path: '/user',
    handler: handler.getUser,
    options: {
      auth: 'eshop_jwt',
    },
  },

  ....
];
```

Dengan begini kita tidak perlu lagi menambahkan params user id ketika ingin mengambil data user.

Jangan lupa untuk menyimpan perubahan ke dalam Git dan push ke GitHub dengan menjalankan perintah berikut

```bash
git add .
git commit -m "menambahkan authorization"
git push
```

**[<< Sebelumnya](m9-tokenization.md)** | **[Selanjutnya >>](m11-carts.md)**
