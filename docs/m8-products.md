# Products

# Migration create-table-products

Jalankan perintah berikut untuk membuat table `products` dengan migration

```bash
npm run migration add migration create-table-products
```

Maka file `<timestamp>_create-table-products.jd`. Buka dan tambahkan kode berikut

```js
module.exports = {
  "up": `CREATE TABLE products (
      id VARCHAR(50) NOT NULL,
      title TEXT NOT NULL,
      price INTEGER NOT NULL,
      description TEXT,
      image TEXT,
      PRIMARY KEY (id)
  )`,
  "down": "DROP TABLE products"
}
```

Setelah itu jalankan perintah berikut untuk membuat table products

```bash
npm run migration up
```

Maka table products siap digunakan

# ProductsService

Buatlah file dengan nama `ProductsService.js` pada folder `services/mysql` dan buat class serta coonstructor nya

```js
class ProductsService {
  #database;

  constructor(database) {
    this.#database = database;
  }
}

module.exports = ProductsService;
```

Sekarang kita buat service untuk menambahkan produk baru (untuk saat ini user pun bisa menambahkan produk)

```js
class ProductsService {
  ...

  async addProduct(title, price, description) {
    const id = `product-${nanoid(16)}`
    const query = `INSERT INTO products (id, title, price, description, image)
      VALUES (
        '${id}',
        '${title}',
        ${price},
        '${description}',
        NULL
      )`;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows  < 1) {
      throw new InvariantError('Gagal menambahkan produk')
    }

    return id;
  }

  ...
}
```

Selanjutnya method untuk mengambil semua produk

```js
class ProductsService {
  ...

  async getAllProducts() {
    const query = 'SELECT * FROM products';

    const result = await this.#database.query(query);

    return result;
  }

  ...
}
```

Lalu kita buat method untuk mengambil detail produk sesuai dengan id nya

```js
class ProductsService {
  ...

  async getProductById(id) {
    const query = `SELECT * FROM products WHERE id = '${id}'`;

    const result = await this.#database.query(query);

    if(!result || result.length < 1 || result.affectedRows < 1) {
      throw new NotFoundError('Produk tidak ditemukan');
    }

    return result[0];
  }

  ...
}
```

Mantap!. Lanjut ke memperbarui data produk

```js
class ProductsService {
  ...

  async updateProductById(id, {title, price, description}) {
    const queryProduct = `SELECT id FROM products WHERE id = '${id}'`;

    const product = await this.#database.query(queryProduct);

    if (!product || product.length < 1 || product.affectedRows < 1) {
      throw new NotFoundError('Produk tidak ditemukan');
    }

    const query = `UPDATE products SET 
        title = '${title}',
        price = ${price},
        description = '${description}'
      WHERE id = '${id}'`;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new InvariantError('Gagal memperbarui produk');
    }
  }

  ...
}
```


Terakhir method untuk menghapus produk

```js
class ProductsService {
  ...

  async deleteProductById(id) {
    const query = `DELETE FROM products WHERE id = '${id}'`;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new NotFoundError('Gagal menghapus produk, id tidak ditemukan');
    }
  }

  ...
}
```

Mantap, semua service sudah siap digunakan

> Untuk data **image** akan kita ubah saat mengimplementasikan fitur upload file

# ProductsValidator

Next, kita lanjut untuk membuat valodator payload/body nya. Buat terlebih dahulu folder `products` di dalam `validator` dan buat file `schema.js` beserta `index.js` nya.

Untuk schema nya ikuti kode berikut ini

```js
const Joi = require('joi');

const ProductsPayloadSchema = Joi.object({
  title: Joi.string().required(),
  price: Joi.number().required(),
  description: Joi.string().allow('', null),
});

module.exports = { ProductsPayloadSchema };
```

Lnjut ke file `index.js` nya

```js
const InvariantError = require('../../exceptions/InvariantError');
const { ProductsPayloadSchema, ProductImageHeaderSchema } = require('./schema');

const ProductsValidator = {
  validateProductsPayload: (payload) => {
    const validationResult = ProductsPayloadSchema.validate(payload);
    if (validationResult.error) {
      throw new InvariantError(validationResult.error.message);
    }
  },
};

module.exports = ProductsValidator;
```

Siipp, validator sudah siap digunakan. Yuk kita lanjut.

# Plugin Products (routes, handler)

Pertama kita buat folder `products` terlebih dahulu di dalam `api`, lalu buat file `routes.js`, `handler.js` dan `index.js`.

Di dalam `routes.js` kita tentukan rute beserta method nya

```js
const path = require('path');

const routes = (handler) => [
  {
    method: 'POST',
    path: '/products',
    handler: handler.postProduct,
  },
  {
    method: 'GET',
    path: '/products',
    handler: handler.getProducts,
  },
  {
    method: 'GET',
    path: '/products/{id}',
    handler: handler.getProductById,
  },
  {
    method: 'PUT',
    path: '/products/{id}',
    handler: handler.putProductById,
  },
  {
    method: 'DELETE',
    path: '/products/{id}',
    handler: handler.deleteProductById,
  },
];

module.exports = routes;
```

Mantaps, route dan method nya sudah kita tentukan

Sekarang kita beralih ke file `handler.js` yah. Buat class, constructor, property beserta nama method nya terlebih dahulu. Pastikan nama method nya sesuai yang ada di routes yaa.

```js
class ProductsHandler {
  #service;
  #validator;

  constructor(service, validator) {
    this.#service = service;
    this.#validator = validator;

    this.postProduct = this.postProduct.bind(this);
    this.getProducts = this.getProducts.bind(this);
    this.getProductById = this.getProductById.bind(this);
    this.putProductById = this.putProductById.bind(this);
    this.deleteProductById = this.deleteProductById.bind(this);
  }

  async postProduct(request, h) {}

  async getProducts(request, h) {}

  async getProductById(request, h) {}

  async putProductById(request, h) {}

  asyn deleteProductById(request, h) {}
}

module.exports = ProductsService;
```

Setelah itu lengkapi handler dengan mengikuti kode seperti di bawah ini

```js
class ProductsHandler {
  ....

  async postProduct(request, h) {
    this.#validator.validateProductsPayload(request.payload);
    const { title, price, description } = request.payload;

    const productId = await this.#productsService.addProduct(title, price, description);

    const response = h.response({
      status: 'success',
      message: 'Produk berhasil ditambahkan',
      data: {
        productId,
      },
    });
    response.code(201);
    return response;
  }

  async getProducts(request, h) {
    const products = await this.#productsService.getAllProducts();

    return {
      status: 'success',
      message: 'Data produk berhasil diambil',
      data: {
        products,
      }
    };
  }

  async getProductById(request, h) {
    const { id } = request.params;

    const product = await this.#productsService.getProductById(id);

    return {
      status: 'success',
      message: 'Data produk berhasil diambil',
      data: {
        product,
      },
    };
  }

  async putProductById(request, h) {
    this.#validator.validateProductsPayload(request.payload);
    const { id } = request.params;
    const { title, price, description } = request.payload;

    await this.#productsService.updateProductById(id, { title, price, description });

    return {
      status: 'success',
      message: 'Produk berhasil diperbarui',
    };
  }

  async deleteProductById(request, h) {
    const { id } = request.params;

    await this.#productsService.deleteProductById(id);

    return {
      status: 'success',
      message: 'Produk berhasil dihapus',
    };
  }

  ....
}
```

Selanjutnya buka file `index.js` dan ikuti kode berikut

```js
const ProductsHandler = require('./handler');
const routes = require('./routes');

module.exports = {
  name: 'products',
  version: '1.0.0',
  register: async (server, { service, validator }) => {
    const handler = new ProductsHandler(service, validator);
    server.route(routes(handler));
  },
};
```

Mantaps, sekarang saatnya kita memasang plugin di server. Buka file `server.js` dan tambahkan kode berikut

```js
// products
const products = require('./api/products');
const ProductsService = require('./services/mysql/ProductsService');
const ProductsValidator = require('./validator/products');

const init = () => {
  const database = new Database();
  ...
  const productsService = new ProductsService(database);

  .....

  //  register internal plugin
  await server.register([
    ....
    {
      plugin: products,
      options: {
        service: productsService,
        validator: ProductsValidator,
      }
    },
    ....
  ]);

};
```

Good job! Kita telah selesai menambahkan fitur products, saatnya untuk mencobanya di Postman.

Selanjutnya jangan lupa simpan perubahan di Git dan push ke GitHub

```bash
git add .
git commit -m "menambahkan fitur products"
git push
```

**[<< Sebelumnya](m7-error-handling.md)** <!--| **[Selanjutnya >>](m9-tokenization.md)** -->