# Carts (Keranjang belanja)

# Migration create-table-carts

Seperti biasa, sebelum kita membuat fitur untuk keranjang belanja, kita harus membuat table carts terlebih dahulu menggunakan metode migration. Untuk itu mari kita buat migration dengan menjalankan perintah berikut

```bash
npm run migration add migration create-table-carts
```

Maka file `<timestamp>_create-table-carts.js` otomatis akan ditambahkan di dalam folder `migrations`. Buka dan tambahkan kode berikut

```js
module.exports = {
  "up": `CREATE TABLE carts (
      id VARCHAR(50) NOT NULL,
      userId VARCHAR(50) NOT NULL,
      productId VARCHAR(50) NOT NULL,
      quantity INTEGER NOT NULL,
      PRIMARY KEY (id))`,
  "down": "DROP TABLE carts"
}
```

Setelah itu kita jalankan migrasi agar bisa membuat table carts. Ikuti perintah berikut

```bash
npm run migration up
```

Maka table `carts` siap digunakan

# CartsService

Seperti biasa, langkah pertama untuk membuat fitur carts adalah membuat **service** terlebih dahulu. Untuk itu mari kita buat file dengan nama `CartsService.js` di dalam folder `services`, dan ikutilah kode nya seperti berikut.

Import dependency/library/modul yang diperlukan terlebih dahulu

```js
const { nanoid } = require('nanoid');
const InvariantError = require('../../exceptions/InvariantError');
const NotFoundError = require('../../exceptions/NotFoundError');
const AuthorizationError = require('../../exceptions/AuthorizationError');
```

Setelah itu buatlah class beserta constructornya yang akan menerima database, lalu jangan lupa untuk di **exports**

```js
class CartsService {
  #database;

  constructor(database) {
    this.#database = database
  }
}

module.exports = CartsService;
```

Di bawah method constructor kita tambahkan method yang akan menangani method menambahkan item ke keranjang. Berikut kode nya

```js
class CartsService {
  ....

  async addCart(userId, productId, quantity) {
    const queryUser = `SELECT id FROM users WHERE id = '${userId}'`;

    const user = await this.#database.query(queryUser);

    if (!user || user.length < 1 || user.affectedRows < 1) {
      throw new NotFoundError('Gaal menambahkan item ke keranjang, user tidak ditemukan');
    }

    const id = `item-${nanoid(16)}`;
    const query = `INSERT INTO carts (id, productId, userId, quantity)
        VALUES (
          '${id}',
          '${productId}',
          '${userId}',
          ${quantity}
        )
    `;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new InvariantError('Gagal menambahkan item ke keranjang');
    }
  }

  ....
}
```

Sebalum kita lanjut, pertama kita buat methood untuk memverifikasi apakah user tersebut merupakan pemilik keranjang tersebut atau tidak.

```js
class CartsService {
  ....

  async #verifyCartOwner(userId, itemId) {
    const queryItem = `SELECT id FROM carts WHERE id = '${itemId}'`;

    const item = await this.#database.query(queryItem);

    if (!item || item.length < 1 || item.affectedRows < 1) {
      throw new NotFoundError('Item tidak ditemukan');
    }

    const query = `SELECT id FROM carts WHERE userId = '${userId}' AND id = '${itemId}'`;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new AuthorizationError('Anda tidak dapat mengubah ini');
    }
  }

  ....
}
```

Mantap, setelah itu kita akan membuat method untuk mengambil semua item dari keranjang belanja yang ditambahkan oleh user (jadi hanya menampilkan daftar item yang hanya dimiliki oleh user yang meminta data saja). Untuk mengambil data keranjang belanja dengan mengambil informasi produknya, kita akan menggunakan query **JOIN**.

```js
class CartsService {
  ....

  async getCartByUserId(userId) {
    const queryUser = `SELECT id FROM users WHERE id = '${userId}'`;

    const user = await this.#database.query(queryUser);

    if (!user || user.length < 1 || user.affectedRows < 1) {
      throw new NotFoundError('Gagal mengambil keranjang, user tidak ditemukan');
    }

    const query = `
      SELECT carts.id, 
        products.id AS productId, products.title, products.price, products.image,
        carts.quantity
      FROM products JOIN carts JOIN users
      ON carts.productId = products.id
      AND carts.userId = users.id
      WHERE carts.userId = '${userId}'
    `;

    const result = await this.#database.query(query);

    return result;
  }

  ....
}
```

Dan sekarang kita akan membuat method untuk mengubah item yang ada di keranjang. Sebetulnya yang bisa user ubah itu hanya jumlah item (quantity).

```js
class CartsService {
  ....

  async updateCartByItemId(userId, itemId, qty) {
    await this.#verifyCartOwner(userId, itemId);

    const query = `UPDATE carts SET 
        quantity = ${qty}
      WHERE id = '${itemId}' AND userId = '${userId}'
    `;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new InvariantError('Gagal memperbarui item di keranjang');
    }
  }

  ....
}
```

Terakhir kita akan membuat method untuk menghapus item yang ada di keranjang.

```js
class CartsService {
  ....

  async deleteCartByItemId(userId, itemId) {
    await this.#verifyCartOwner(userId, itemId);

    const query = `DELETE FROM carts WHERE id = '${itemId}' AND userId = '${userId}'`;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new InvariantError('Gagal menghapus item di keranjang');
    }
  }

  ....
}
```

# CartsValidator

Sekarang kita lanjutkan untuk membuat validator nya. Seperti biasa, buatlah folder `carts` di dalam folder `validator`, setelah itu buat file `index.js` dan `schema.js` di dalam folder carts.

Pertama kita buka file `schema.js` terlebih dahulu dan masukan kode berikut

```js
const Joi = require('joi');

// method ini digunakan untuk memvalidasi inputan user
// saat menambahkan item ke keranjang
const CartsPayloadSchema = Joi.object({
  productId: Joi.string().required(),
  quantity: Joi.number().min(1).required(),
});

// method ini digunakan untuk memvalidasi inputan user
// saat mengubah jumlah quantity item di keranjang
const CartsQuerySchema = Joi.object({
  qty: Joi.number().min(1).required(),
});

module.exports = { CartsPayloadSchema, CartsQuerySchema };
```

Selanjutnya kita beralih ke file `index.js` dan masukan kode berikut

```js
const InvariantError = require('../../exceptions/InvariantError');
const { CartsPayloadSchema, CartsQuerySchema } = require('./schema');

const CartsValidator = {
  // untuk memvalidasoi inputan user saat menambahkan item ke keranjang
  validateCartsPayload: (payload) => {
    const validationResult = CartsPayloadSchema.validate(payload);
    if (validationResult.error) {
      throw new InvariantError(validationResult.error.message);
    }
  },
  // untuk memvalidasoi inputan user saat mengubah jumlah quantity item di keranjang
  validateCartsQuery: (query) => {
    const validationResult = CartsQuerySchema.validate(query);
    if (validationResult.error) {
      throw new InvariantError(validationResult.error.message);
    }
  },
};

module.exports = CartsValidator;
```

# Plugin Carts (routes, handler)

Sip. Sekarang mari kita lanjutkan untuk membuat plugin nya, mulai dari membuat **routes** dan **handler** nya. Pertama kita buat folder `carts` di dalam folder `api`, setelah itu buat file `index.js`, `routes.js` dan `handler.js` di dalam folder carts.

Pertama kita buka file `routes.js` terlebih dahulu, dan masukan kode berikut

```js
const routes = (handler) => [
  {
    method: 'POST',
    path: '/carts',
    handler: handler.postCart,
    options: {
      auth: 'eshop_jwt',
    },
  },
  {
    method: 'GET',
    path: '/carts',
    handler: handler.getCartByUserId,
    options: {
      auth: 'eshop_jwt',
    },
  },
  {
    method: 'PUT',
    path: '/carts/{itemId}',
    handler: handler.putCartByItemId,
    options: {
      auth: 'eshop_jwt',
    },
  },
  {
    method: 'DELETE',
    path: '/carts/{itemId}',
    handler: handler.deleteCartByItemId,
    options: {
      auth: 'eshop_jwt',
    },
  },
];

module.exports = routes;
```

Sekarang saatnya kita membuat handler nya. Pertama kita buka file `handler.js` terlebih dahulu, dan masukan kode berikut. Buat class beserta constructor dan buat method yang akan digunakan sesuai yang tertera di routes.

```js
class CartsHandler {
  #service;
  #validator;

  constructor(service, validator) {
    this.#service = service;
    this.#validator = validator;

    this.postCart = this.postCart.bind(this);
    this.getCartByUserId = this.getCartByUserId.bind(this);
    this.putCartByItemId = this.putCartByItemId.bind(this);
    this.deleteCartByItemId = this.deleteCartByItemId.bind(this);
  }

  async postCart(request, h) {}

  async getCartByUserId(request, h) {}

  async putCartByItemId(request, h) {}

  async deleteCartByItemId(request, h) {}
}

module.exports = CartsHandler;
```

Sekarang kita buat method untuk menambahkan item ke keranjang, yaitu **postCart**. Ubah method ini seperti berikut.

```js
class CartsHandler {
  ....

  async postCart(request, h) {
    this.#validator.validateCartsPayload(request.payload);
    const { id: userId } = request.auth.credentials;
    const { productId, quantity } = request.payload;

    await this.#service.addCart(userId, productId, quantity);

    const response = h.response({
      status: 'success',
      message: 'Keranjang berhasil ditambahkan',
    });
    response.code(201);
    return response;
  }

  ....
}
```

Lanjut ke method **getCartByUserId**. Ubah method ini seperti berikut.

```js
class CartsHandler {
  ....

  async getCartByUserId(request, h) {
    console.log(request.auth);
    const { id: userId } = request.auth.credentials;

    const cart = await this.#service.getCartByUserId(userId);
    let subTotal = 0;
    let totalItem = 0;

    cart.forEach((item) => {
      subTotal += item.price;
      totalItem += item.quantity;
    });

    return {
      status: 'success',
      message: 'Data keranjang berhasil diambil',
      data: {
        userId,
        totalItem,
        subTotal,
        cart,
      },
    };
  }

  ....
}
```

Landkut ke method **putCartByItemId**. Ubah method ini seperti berikut.

```js
class CartsHandler {
  ....

  async putCartByItemId(request, h) {
    const { id: userId } = request.auth.credentials;
    const { itemId } = request.params;

    this.#validator.validateCartsQuery(request.query);
    const { qty } = request.query;

    await this.#service.updateCartByItemId(userId, itemId, qty);

    return {
      status: 'success',
      message: 'Item dalam keranjang berhasil diperbarui',
    };
  }

  ....
}
```

Terakhir kita buat method untuk menghapus item dari keranjang, yaitu **deleteCartByItemId**. Ubah method ini seperti berikut.

```js
class CartsHandler {
  ....

  async deleteCartByItemId(request, h) {
    const { id: userId } = request.auth.credentials;
    const { itemId } = request.params;

    await this.#service.deleteCartByItemId(userId, itemId);

    return {
      status: 'success',
      message: 'Item dalam keranjang berhasil dihapus',
    };
  }

  ....
}
```

Maka kita telah menyelesaikan handler nya. Selanjutnya buka file `index.js` untuk membuat plugin pada fitur carts.

```js
const CartsHandler = require('./handler');
const routes = require('./routes');

module.exports = {
  name: 'carts',
  version: '1.0.0',
  register: async (server, { service, validator }) => {
    const handler = new CartsHandler(service, validator);
    server.route(routes(handler));
  },
};
```

Plugin selesai dan siap dipasangkan/register di server

Buka file `server.js` dan tambahkan kode berikut.

```js
....

// carts
const carts = require('./api/carts');
const CartsService = require('./services/mysql/CartsService');
const CartsValidator = require('./validator/carts');

...

const init = async () => {
  const service = new CartsService(database);

  ...

  //  register internal plugin
  await server.register([
    ...
    {
      plugin: carts,
      options: {
        service: cartsService,
        validator: CartsValidator,
      },
    },
    ...
  ]);
}
```

Mantap, kita telah menyelesaikan fitur carts. Saatnya untuk membuktikan apakah berhasil dijalankan atau tidak dengan mencobanya di postman.

Buka postman dan makukan request ke url `http://localhost:3000/carts` dengan method `POST`.

```json
{
  "productId": "1", // untuk productId sesuaikan dengan produk yang ada, dan jika produknya tidak ada, harusnya mengembalikan error
  "quantity": 1
}
```

Simpan perubahan ke Git dan push ke GitHub dengan menjalankan perintah berikut

```bash
git add .
git commit -m "menambahkan fitur carts"
git push
```

**[<< Sebelumnya](m10-authorization.md)** | **[Selanjutnya >>](m11-carts.md)**