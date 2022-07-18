# Transactions

Sekarang saatnya kita membuat fitur untuk checkout dan transaksi. Alur transaksi adalah jika user/client menambahkan item produk ke dalam keranjang (carts) lalu memilih untuk checkout, maka **ssemua** produk yang ada dalam keranjang tersebut akan di proses untuk membuat transaksi, dan keranjang belanja nya akan dikosongkan.

Untuk membuat transaksi kita membutuhkan dua table, pertama table `transactions` yang hanya memuat informasi transaksi yang dilakukan yaitu siapa yang membuat transaksi dan pada tanggal berapa transaksi dibuat. Dan table yang kedua yaitu `orders` yang memuat informasi produk yang di checkout oleh user yang memiliki relasi dengan table **transaction**.

# Migration create-table-transactions

Pertama kita akan membuat migration untuk table `transactions`. Untuk membuatnya jalankan perintah berikut:

```bash
npm run migration add migration create-table-transactions
```

Maka file `<timestamp>_create-table-transactions.js` akan otomatis dibuat. Buka file tersebut dan tambahkan kode berikut

```js
module.exports = {
  "up": `CREATE TABLE transactions (
    id VARCHAR(50) NOT NULL,
    userId VARCHAR(50) NOT NULL,
    dateCreated TEXT NOT NULL,
    PRIMARY KEY (id)
  )`,
  "down": "DROP TABLE transactions"
}
```

# Migration create-table-orders

Selanjutnya kita akan membuat migration untuk table `orders`. Untuk membuatnya jalankan perintah berikut:

```bash
npm run migration add migration create-table-orders
```

Maka file `<timestamp>_create-table-orders.js` akan otomatis dibuat. Buka file tersebut dan tambahkan kode berikut

```js
module.exports = {
  "up": `CREATE TABLE orders (
    id VARCHAR(50) NOT NULL,
    productId VARCHAR(50) NOT NULL,
    userId VARCHAR(50),
    transactionId VARCHAR(50) NOT NULL,
    quantity INTEGER NOT NULL,
    PRIMARY KEY (id)
  )`,
  "down": "DRAP TABLE orders"
}
```

Selanjutnya jalankan migration dengan perintah berikut untuk membuat table transactions dan orders

```bash
npm run migration up
```

Maka kedua file migration tersebut akan otomatis dijalankan dan database akan terupdate. Maka dengan begitu table transactions dan orders sudah dibuat dan siap digunakan.

# TransactionsService

Kita akan membuat service untuk transaksi. Buatlah file `TransactionsService.js` di dalam folder `services/mysql` dan buat class serta constructor nya dan jangan lupa untuk meng-export.

```js
class TransactionsService {
  #database;

  constructor(database) {
    this.#database = database;
  }
}

module.exports = TransactionsService;
```

Selanjutnya kita akan membuat method untuk menambahkan transaksi (checkout). Mohon untuk diperhatikan karena kita akan menjalankan banyak query ke database. Untuk itu langsung saja kita buat method** addCheckout()**.

```js
class TransactionsService{
  ....

  async addCheckout(userId) {
    // mengambil data user, apakah terdapat di database
    const queryUser = `SELECT id FROM users WHERE id = '${userId}'`;
    const user = await this.#database.query(queryUser);

    // jika tidak ada user dengan id tersebut, maka tidak bisa melakukan checkout
    if (!user || user.length < 1 || user.affectedRows < 1) {
      throw new NotFoundError('Gagal checkout, user tidak ditemukan');
    }

    // mengambil data cart dari user tersebut, apakah terdapat di database
    const queryCarts = `SELECT productId, quantity FROM carts WHERE userId = '${userId}'`;
    const carts = await this.#database.query(queryCarts);

    // jika tidak ada cart dari user tersebut, maka tidak bisa melakukan checkout
    if (!carts || carts.length < 1 || carts.affectedRows < 1) {
      throw new NotFoundError('Gagal checkout, tidak terdapat item apapun di keranjang');
    }

    // membuat transaksi dengan memasukan data id user dan waktu checkout
    const transactionId = `transaction-${nanoid(16)}`;
    const queryTransaction = `INSERT INTO transactions (id, userId, dateCreated) VALUES (
      '${transactionId}',
      '${userId}',
      '${new Date().toLocaleString()}'
    )`;
    const transaction = await this.#database.query(queryTransaction);

    // jika gagal kembalikan error
    if (!transaction || transaction.length < 1 || transaction.affectedRows < 1) {
      throw new InvariantError('Gagal membuat transaksi');
    }

    /**
     * Perhatikan kode di bawah
     * Kita akan me looping setiap item yang ada di cart dan memasukan data tersebut ke dalam table orders
     * Jadi sebetulnya table orders itu seperti table carts, namun untuk item yang sudah di checkout.
     * Jangan lupa untuk menambahkan id transaksi sebagai relasinya dengan table transactions
     **/
    carts.forEach(async (product) => {
      const id = `orderItem-${nanoid(16)}`;
      const query = `INSERT INTO orders (id, productId, userId, transactionId, quantity) VALUES (
        '${id}',
        '${product.productId}',
        '${userId}',
        '${transactionId}',
        '${product.quantity}'
      )`;

      const result = await this.#database.query(query);

      if (!result || result.length < 1 || result.affectedRows < 1) {
        throw new InvariantError('Transaksi gagal');
      }
    });

    // jika berhasil, hapus semua item yang ada di carts
    await this.#database.query(`DELETE FROM carts WHERE userId = '${userId}'`);

    return transactionId;
  }

  ....
}
```

Langkah selanjutnya kit akan membuat method untuk mengambil semua data transaksi yang dibuat oleh user. Namun kita hanya akan mengambil id transaksi dan tanggal dibuat nya saja. Untuk itu kita buat method **getTransactionsByUserId()**.

```js
class TracsactionsService {
  ....

  async getTransactionsByUserId(userId) {
    const query = `SELECT id, dateCreated FROM transactions WHERE userId = '${userId}'`;

    const result = await this.#database.query(query);

    return result;
  }

  ....
}
```

Terakhir kita akan mengambil data detail transaksi. Namun sebelum itu kita perlu memverifikasi apakah user yang mengakses data ini adalah yang bersangkutan (pemilik transaksi) dengan membuat method **#verifyTransactionOwner()** terlebih dahulu

> Hal ini juga dilakukan pada fitur carts dengan **meng authorization** user

```js
class TransactionsService {
  ....

  async #verifyTransactionOwner(userId, transactionId) {
    const queryItem = `SELECT id FROM transactions WHERE id = '${transactionId}'`;

    const transaction = await this.#database.query(queryItem);

    if (!transaction || transaction.length < 1 || transaction.affectedRows < 1) {
      throw new NotFoundError('Transaksi tidak ditemukan');
    }

    const query = `SELECT id, dateCreated FROM transactions WHERE id = '${transactionId}' AND userId = '${userId}'`;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new AuthorizationError('Transaksi tidak ditemukan, anda tidak mempunyai hak untuk mengakses ini');
    }

    return result[0];
  }

  ....
}
```

Maka sekarang kita bisa membuat method untuk mengambil data detail transaksi. ***getTransactionById()***.

```js
class TracsactionsService {
  ....

  async getTransactionById(userId, transactionId) {
    const transaction = await this.#verifyTransactionOwner(userId, transactionId);

    // untuk mengambil detail item yang ada di transaksi ini
    // kita akan menggunakan query JOIN table orders dengan table products
    // sesuai dengan id product yang ada di table orders
    const query = `
      SELECT products.title, products.price, products.image,
        orders.quantity
      FROM orders JOIN products
      ON orders.productId = products.id
      WHERE orders.transactionId = '${transaction.id}'
      AND orders.userId = '${userId}'
    `;

    const orders = await this.#database.query(query);

    if (!orders || orders.length < 1 || orders.affectedRows < 1) {
      throw new InvariantError('Transaksi gagal dimuat');
    }

    return {
      transaction,
      orders,
    };
  }

  ....
}
```

Mantap!. Kita telah selesai membuat TransactionService yang siap kita gunakan. Selanjutnya kita tidak memerlukan validator karena kita tidak memerlukan user mengirim data ke server. Untuk itu kita bisa lanjutkan ke pembuatan plugin.

# Plugin Transactions (routes, handler)

Pertama buatlah folder `transactions` di dalam folder `api`. Setelah itu buat file `index.js`, `routes.js` dan `handler.js` di dalam folder tersebut.

Sekarang kita akan ke `routes.js` terlebih dahulu. Ikuti kode berikut

```js
const routes = (handler) => [
  {
    method: 'POST',
    path: '/checkout',
    handler: handler.postCheckout,
    options: {
      auth: 'eshop_jwt',
    },
  },
  {
    method: 'GET',
    path: '/transactions',
    handler: handler.getTransactions,
    options: {
      auth: 'eshop_jwt',
    },
  },
  {
    method: 'GET',
    path: '/transactions/{id}',
    handler: handler.getTransactionById,
    options: {
      auth: 'eshop_jwt',
    },
  },
];

module.exports = routes;
```

Good!. Sekarang buka file `handler.js`. Buatlah class `TransactionsHandler` beserta constructor nya. Setelah itu buatlah method kosong `postCheckout()`, `getTransactions()` dan `getTransactionById()`. Jangan lupa di exports.

```js
class TransactionsHandler {
  #service;

  constructor(service) {
    this.#service = service;

    this.postCheckout = this.postCheckout.bind(this);
    this.getTransactions = this.getTransactions.bind(this);
    this.getTransactionById = this.getTransactionById.bind(this);
  }

  async postCheckout(request, h) {}

  async getTransactions(request, h) {}

  async getTransactionById(request, h) {}

}

module.exports = TransactionsHandler;
```

Sekarang ubah method `postCheckout()` menjadi seperti ini

```js
class TransactionsHandler {
  ....

  async postCheckout(request, h) {
    // kita mengambil data user yang sedang login
    // dari token yang dikirimkan
    const { id: userId } = request.auth.credentials;

    const transactionId = await this.#service.addCheckout(userId);

    const response = h.response({
      status: 'success',
      message: 'Transaksi berhasil',
      data: {
        transactionId,
      },
    });
    response.code(201);
    return response;
  }

  ....
}
```

Selanjutnya ke method `getTransactions()`

```js
class TransactionsHandler {
  ....

  async getTransactions(request, h) {
    const { id: userId } = request.auth.credentials;
    const transactions = await this.#service.getTransactionsByUserId(userId);

    return {
      status: 'success',
      message: 'Data transaksi berhasil diambil',
      data: {
        transactions,
      },
    };
  }

  ....
}
```

Terakhir kita buat method `getTransactionById()`

```js
class TransationsHandler {
  ....

  async getTransactionById(request, h) {
    const { id: userId } = request.auth.credentials;
    const { id } = request.params;

    const { transaction, orders } = await this.#service.getTransactionById(userId, id);
    let total = 0;
    let totalItem = 0;

    orders.forEach((order) => {
      total += order.price;
      totalItem += order.quantity;
    });

    return {
      status: 'success',
      message: 'Data transaksi berhasil diambil',
      data: {
        userId,
        transactionId: id,
        dateCreated: transaction.dateCreated,
        totalItem,
        total,
        orders,
      },
    };
  }

  ....
}
```

Mantap. Kita telah membuat handler, saatnya kita membuat plugin untuk fitur transactions dengan membuka file `index.js` di dalam folder `transactions`.

```js
const TransactionsHandler = require('./handler');
const routes = require('./routes');

module.exports = {
  name: 'transactions',
  version: '1.0.0',
  register: async (server, { service }) => {
    const handler = new TransactionsHandler(service);
    server.route(routes(handler));
  },
};
```
> Jangan lupa yah, kita hanya membuat service tanpa validator.

Selanjutnya kita pasang/register ke dalam server. Maka dari itu bukalah file `server.js`

```js
...

// transactions
const transactions = require('./api/transactions');
const TransactionsService = require('./services/mysql/TransactionsService');

...

const init = async () => {
  ...
  const transactionsService = new TransactionsService(database);

  ...

  //  register internal plugin
  await server.register([
    ....
    {
      plugin: transactions,
      options: {
        service: transactionsService,
      },
    },
    ....
  ]);

  ....

}
```

Sekarang tibalah saatnya untuk memastikan apakah kita berhasil membuat plugin untuk fitur transactions. Bukalah Postman dan coba untuk menambahkan item kedalam keranjang, setelah itu panggil `http://localhost:5000/checkout` dengan method `POST`.

Simpan perubahan ke Git dan push ke GitHub dengan menjalankan perintah berikut

```bash
git add .
git commit -m "menambahkan fitur transactions"
git push
```

**[<< Sebelumnya](m11-carts.md)** | **[Selanjutnya >>](m13-upload.md)**
