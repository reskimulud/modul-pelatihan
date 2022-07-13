# REST Web Service

REST atau **RE**presentational **S**tate **T**ransfer adalah salah satu gaya arsitektur yang dapat diadaptasi ketika membangun web service. Arsitektur ini sangat populer digunakan karena pengembangannya yang relatif mudah. REST menggunakan pola request-response dalam berinteraksi, artinya ia memanfaatkan protokol HTTP seperti yang sudah kita pelajari di materi sebelumnya. 

Dalam implementasinya arsitektur **REST** benar-benar memisahkan peran client dan server, bahkan keduanya tidak harus saling mengetahui. Artinya ketika terjadi perubahan besar di sisi client, tidak akan berdampak pada sisi server, begitu juga sebaliknya.

# REST atau RESTful API

RESTful merupakan sebutan untuk web services yang menerapkan arsitektur REST. REST juga merupakan **API** (Application Program Interface) karena ia digunakan untuk menjembatani antara sistem yang berbeda (*client* dan *server*). API merupakan antarmuka yang menjadi perantara antara sistem aplikasi yang berbeda. API tak hanya dalam bentuk Web Service, bisa saja berupa SDK (Software Development Kit) ataupun lainnya.

Sebelum membangun REST API, kita harus memahai konsep-konsep penting yang harus diterapkan dalam mengembangkan arsitektur ini, diantaranya :

  * Format request dan response.
  * HTTP Verbs/Methods.
  * HTTP Response code.
  * URL Design.

Mari kita bahas satu per satu.

# Format Request dan Response

Biasanya, format yang digunakan untuk *request* dan *response* menggunakan format **JSON** (JavaScript Object Notation), saat ini JSON merupakan salah satu format standar dalam transaksi data. Namun sebenarnya selain format JSON, terdapat format lain seperti XML, CSV, TXT atau yang lain.

> Agar REST API selalu merespons dengan format JSON, pastikan setiap respons terdapat properti Content-Type dengan nilai application/json.

Seperti namanya, JSON memiliki struktur seperti JavaScript Object yakni menggunakan key-value. Bedanya, key pada JSON selalu dituliskan menggunakan tanda kutip dua (“”). Value pada JSON dapat menampung nilai primitif seperti string, number, boolean, atau nilai non primitif seperti object atau array.

Contoh response dengan format JSON :

```json
{
  "status": "success",
  "message": "User data fetched successfully",
  "data": {
    "id": 1,
    "name": "Reski Mulud Muchamad",
    "email": "reski.mulud@gmail.com",
    "address": "Sukabumi",
    "role_id": 1,
    "role_name": "admin",
  },
}
```

Walaupun memiliki nama JavaScript Object Notation, bukan berarti kita harus menggunakan JavaScript untuk mengolah data dengan format JSON. Format JSON dapat digunakan oleh hampir semua bahasa pemrograman yang ada.

# HTTP Verbs dan Response Code

  * **HTTP Verbs/Method**

Karena REST API menggunakan protokol HTTP, kita dapat memanfaatkan HTTP verbs untuk menentukan aksi.

`GET` untuk mendapatkan data, `POST` untuk mengirimkan data baru, `PUT` untuk memperbarui data yang ada, dan `DELETE` untuk menghapus data. Verbs tersebutlah yang umum digunakan dalam operasi **CRUD**.

  * **Response Code**

Status-Line merupakan salah satu bagian dari HTTP Response. Di dalam status line terdapat response code yang mengindikasikan bahwa permintaan yang client lakukan berhasil atau tidak. Karena itu, ketika membangun REST API kita perlu memperhatikan dan menetapkan response code secara benar.

Status code bernilai 3 digit angka. Pada REST API, berikut nilai-nilai status code yang sering digunakan:

  - **200 (OK)** - Permintaan client berhasil dijalankan oleh server.
  - **201 (Created)** - Server berhasil membuat/menambahkan resource yang diminta client.
  - **400 (Bad Request)** - Permintaan client gagal dijalankan karena proses validasi input dari client gagal.
  - **401 (Unauthorized)** - Permintaan client gagal dijalankan. Biasanya ini disebabkan karena pengguna belum  melakukan proses autentikasi.
  - **403 (Forbidden)** - Permintaan client gagal dijalankan karena ia tidak memiliki hak akses ke resource yang diminta.

# URL Design

URL, Path, atau Endpoint merupakan salah satu bagian terpenting yang harus diperhatikan ketika membangun REST API. Dengan merancang endpoint yang baik, penggunaan API akan lebih mudah dipahami. Dalam merancang endpoint, ikutilah aturan umum atau convention agar penggunaan API kita memiliki standar yang diharapkan oleh banyak developer.

Contoh struktur URL :

`https://api.reskimulud.my.id/portfolio/1`

  * `https://` - adalah protokol
  * `api.reskimulud.my.id` - base URL
  * `/porfolio` - endpoint atau PATH
  * `/1` - paarameter

**[<< Sebelumnya](m1-intro-backend.md)** | **[Selanjutnya >>](m3-rest-hapi.md)**