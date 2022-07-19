# Upload File ke Server

Jika sebelumnya kita hanya mengirim data ke server dalam bentuk text, sekarang kita akan mengitim data berupa file. Untuk studi kasus kita kali ini akan mengirimkan gambar produk. Tidak perlu berlama-lama, mari kita praktikan.

# StorageService

Sebelumnya kita hanya berfokus untuk membuat service yang menangani proses pengambilan data dari database, sekarang kita akan membuat service yang menangani proses pengiriman data ke penyimpanan lokal di server. Buatlah folder `storage` di dalam `services` dan buat file `StorageService.js`. Buka file tersebut dan ikuti kode berikut

```js
const fs = require('fs');

class StorageService {
  #folder;

  constructor(folder) {
    this.#folder = folder;

    if (!fs.existsSync(folder)) {
      fs.mkdirSync(folder, {recursive: true});
    }
  }

  // method untuk membuat file baru ke dalam server
  writeFile(file, meta, nameId) {
    const fileExt = meta.filename.split('.').pop();

    const filename = `${nameId}.${fileExt}`;
    const path = `${this.#folder}/${filename}`;

    const fileStream = fs.createWriteStream(path);

    return new Promise((resolve, reject) => {
      fileStream.on('error', (error) => reject(error));
      file.pipe(fileStream);
      file.on('end', () => resolve(filename));
    });
  }

  // method untuk menghapus file dari server
  deleteFile(filename) {
    fs.unlinkSync(`${this.#folder}/${filename}`);
  }
}

module.exports = StorageService;
```

# Ubah ProductsService

Selain mengupload file ke server, kita juga akan menyimpan nama file tersebut ke database. Maka dari itu ubahlah file `ProductsService.js` nya dan tambahkan method berikut.

```js
class ProductsService {
  ....

  async updateProductImageById(id, filename) {
    const oldFileName = await this.#database.query(
        `SELECT image FROM products WHERE id = '${id}'`,
    );

    const queryProduct = `SELECT id FROM products WHERE id = '${id}'`;

    const product = await this.#database.query(queryProduct);

    if (!product || product.length < 1 || product.affectedRows < 1) {
      throw new NotFoundError('Produk tidak ditemukan');
    }

    const query = `UPDATE products SET image = '${filename}' WHERE id = '${id}'`;

    const result = await this.#database.query(query);

    if (!result || result.length < 1 || result.affectedRows < 1) {
      throw new InvariantError('Gambar produk gagal diperbarui');
    }

    // method ini akan mengembalikan nama file yang lama
    return oldFileName[0].image;
  }

  ....
}
```

# Ubah ProductsValidator

Sebelum kita mengatur perubahan yang ada di dalam plugin (handler, routes), ubahlah validatornya terlebih dahulu. Bukalah file `schema.js` (folder `products` yah) dan tambahkan kode berikut.

```js
const ProductImageHeaderSchema = Joi.object({
  'content-type': Joi.string().valid(
      'image/apng',
      'image/avif',
      'image/gif',
      'image/jpeg',
      'image/png',
      'image/webp').required(),
}).unknown();

// tambahkan ProductImageHeaderSchema ke dalam exports
module.exports = { ProductsPayloadSchema, ProductImageHeaderSchema };
```

Setelah itu buka file `index.js` (folder `products` yah) dan tambahkan kode berikut.

```js
const ProductsValidator = {
  ....

  validateProductImageHeader: (header) => {
    const validationResult = ProductImageHeaderSchema.validate(header);
    if (validationResult.error) {
      throw new InvariantError(validationResult.error.message);
    }
  },

  ....

};
```

Maka validator siap digunakan

# Method upload gambar di products

Sekarang bukalah file `routes.js` pada folder `api/products` dan tambahkan method berikut untuk mengupload gambar ke server. Jangan lupa import **path**

```js
const path = require('path');

const routes = (handler) => [
  ...

  {
    method: 'PUT',
    path: '/products/{id}/image',
    handler: handler.putProductImageById,
    options: {
      auth: 'eshop_jwt',
      payload: {
        allow: 'multipart/form-data',
        multipart: true,
        output: 'stream',
        maxBytes: 512000,
      },
    },
  },

  ...
];
```
> Kita akan menggunakan `multipart/form-data` untuk mengirimkan file.
> Kenapa kita menggunakan 'PUT'? Karena kita akan mengupdate gambar produk, bukan menambahkan. Maka jika sebelumnya sudah terdapat gambar produk, maka kita akan mengupdate gambar produk tersebut.

# ProductsHandler

Ada beberapa hal yang perlu kita ubah di file `handler.js` (ProductsHandler). Sekarang kita akan menggunakan dua service sekaligus, yaitu `StorageService` dan `ProductsService`. Maka ubahlah konstructor nya

```js
class ProductsHandler {
  #storageService;
  #productsService;
  #validator;

  constructor(storageService, productsService, #validator) {
    this.#storageService = storageService;
    this.#productsService = productsService;
    this.#validator = validator;

    this.postProduct = this.postProduct.bind(this);
    this.getProducts = this.getProducts.bind(this);
    this.getProductById = this.getProductById.bind(this);
    this.putProductById = this.putProductById.bind(this);
    this.deleteProductById = this.deleteProductById.bind(this);
    this.putProductImageById = this.putProductImageById.bind(this);
  }

  ...

  async putProductById(request, h) {}

  ...
}
```

Pasti kita akan menghadapi error karena merubah `#service` menjadi `#storageService`. Untuk mempercepat dalam mengubah `#service` menjadi `#productsService`, kita bisa menekan tombol `Ctrl+R` pada keyboard, ubahlah `#service` menjadi `#productsService`.

Masih di dalam file `handler.js`, kita akan mengubah method `putProductImageById` menjadi seperti berikut.

```js
class ProductsHandler {
  ....

  async putProductImageById(request, h) {
    const { image } = request.payload;
    const { id } = request.params;
    await this.#validator.validateProductImageHeader(image.hapi.headers);

    const nameId = `productImage-${nanoid(16)}`;
    const filename = await this.#storageService.writeFile(image, image.hapi, nameId);
    const oldFileName = await this.#productsService.updateProductImageById(id, filename);

    if (oldFileName != null) {
      await this.#storageService.deleteFile(oldFileName);
    }

    return {
      status: 'success',
      message: 'Gambar produk berhasil diperbarui',
    };
  }

  ....
}
```

Selanjutnya kita perlu memperberui file `index.js` nya pula

```js
module.exports = {
  name: 'products',
  version: '1.0.0',
  // menambahkan opsi untuk mengubah `service` menjadi `productsService`
  // dan menambahkan `storageService`
  register: async (server, { productsService, storageService, validator }) => {
    const handler = new ProductsHandler(productsService, storageService, validator);
    server.route(routes(handler));
  },
};
```

Maka file `server.js` nya akan berubah menjadi seperti berikut

```js
...

// storage
const StorageService = require('./services/storage/StorageService');
const path = require('path');

...

const init () => {
  ....

  const storageService = new StorageService(path.resolve(__dirname, '/api/products/images'));

  ....

  //  register internal plugin
  await server.register([
    ...

    // ubahllah menjadi seperti ini
    {
      plugin: products,
      options: {
        productsService,
        storageService,
        validator: ProductsValidator,
      }
    },

    ...
  ]);
}
```

Sekarang coba lah mengupload gambar di postman (akan dijelaskan di kelas)

Simpan perubahan ke Git dan push ke GitHub dengan menjalankan perintah berikut

```bash
git add .
git commit -m "menambahkan fitur carts"
git push
```

**[<< Sebelumnya](m12-transactions.md)** | **[Selanjutnya >>](m-14-static-file.md)**
