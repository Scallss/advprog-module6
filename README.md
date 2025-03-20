## Commit 1 Reflection

Dalam milestone ini, saya mengimplementasikan web server single-threaded sederhana menggunakan Rust. Tujuannya adalah untuk memahami bagaimana server mendengarkan koneksi TCP yang masuk, menerimanya, dan memproses request HTTP dari browser.

Rincian Implementasi: 
- Menyiapkan TCP Listener:
Saya menggunakan TcpListener::bind("127.0.0.1:7878") untuk membuat listener yang menunggu koneksi pada alamat dan port yang ditentukan.

- Menangani Koneksi Masuk:
Program melakukan iterasi pada koneksi yang masuk dengan menggunakan for stream in listener.incoming(), sehingga setiap request koneksi diterima dan diproses.

- Memproses Request HTTP:
Saya menambahkan fungsi handle_connection. Di dalam fungsi ini, Sebuah BufReader membungkus TcpStream yang masuk untuk membaca request baris per baris. Lalu, Request HTTP dikumpulkan ke dalam vektor string dengan membaca hingga ditemukan baris kosong. Metode ini secara efisien mengumpulkan semua header HTTP yang dikirim oleh browser. Terakhir, Request tersebut kemudian dicetak ke console untuk keperluan debugging.


Saya menyadari bahwa browser terkadang melakukan beberapa koneksi untuk URL yang sama, yang mengakibatkan munculnya  pesan "Connection established!" di console. Hal ini wajar, karena browser sering mencoba ulang request jika respons tidak diterima dengan cepat.

Singkatnya, Latihan ini memperdalam pemahaman saya tentang koneksi TCP dan bagaimana request HTTP disusun dan dikirim melalui koneksi tersebut. Kemudian, penggunaan standard library Rust (khususnya modul std::net dan std::io) memungkinkan saya membuat web server yang sederhana namun efektif. Selain itu, Mencetak request HTTP ke konsol membantu saya memastikan bahwa server berhasil membaca dan menangani koneksi yang masuk.

## Commit 2 Reflection

Pada milestone ini, saya mengembangkan web server sederhana agar dapat mengembalikan halaman HTML sebagai respons terhadap request HTTP dari browser. Beberapa poin penting yang saya pelajari dari perubahan kode pada fungsi `handle_connection` adalah sebagai berikut:

1. **Membaca File HTML**  
   - Fungsi `fs::read_to_string("hello.html").unwrap()` digunakan untuk membaca isi file `hello.html` dan menyimpannya dalam variabel `contents`.  
   - Hal ini memungkinkan server untuk mengirimkan konten HTML yang nantinya akan dirender oleh browser.

2. **Menyusun Respons HTTP**  
   - Respons HTTP disusun dengan format:
     ```
     HTTP/1.1 200 OK
     Content-Length: <panjang_konten>
     
     <isi_dokumen_html>
     ```
   - Penggunaan header `Content-Length` sangat penting agar browser mengetahui ukuran konten yang dikirimkan.

3. **Mengirim Respons ke Browser**  
   - Dengan memanggil `stream.write_all(response.as_bytes()).unwrap()`, seluruh respons HTTP dikirim ke koneksi TCP sehingga browser dapat menampilkannya.
   - Teknik ini memastikan bahwa data dikirim secara utuh dan dalam format yang sesuai dengan protokol HTTP.

4. **Memahami Alur request HTTP**  
   - Kode membaca request HTTP menggunakan `BufReader` dan mengumpulkan setiap baris hingga menemukan baris kosong.  
   - Hal ini memungkinkan pemahaman mengenai bagaimana browser mengirimkan header HTTP dan kapan koneksi seharusnya berhenti membaca.

5. **Pentingnya Lokasi File**  
   - File `hello.html` harus berada di direktori yang sama dengan lokasi eksekusi program agar dapat dibaca dengan benar.  
   - Jika file tidak ditemukan, program akan berhenti dengan error karena penggunaan `.unwrap()`.


Setelah menjalankan `cargo run` dan mengakses `http://127.0.0.1:7878` melalui browser, halaman HTML berhasil ditampilkan. Berikut adalah tangkapan layar dari tampilan browser:

![Commit 2 screen capture](/assets/images/commit2.png)

## Commit 3 Reflection

Pada milestone ini, saya menambahkan fungsionalitas untuk memvalidasi request HTTP dan memberikan respons yang sesuai berdasarkan request tersebut. Beberapa poin pentingnya yaitu:

1. **Validasi request**  
   - Server sekarang memeriksa baris pertama dari request HTTP menggunakan `BufReader::lines().next()`.  
   - Jika baris request sama dengan `"GET / HTTP/1.1"`, maka server menganggap request tersebut valid dan mengembalikan halaman `hello.html`.

2. **Penanganan Respons 404**  
   - Untuk request yang tidak sesuai (misalnya, request ke `/foo` atau URL lainnya), server mengembalikan respons dengan status `HTTP/1.1 404 NOT FOUND`.  
   - Halaman error `404.html` digunakan untuk menginformasikan pengguna bahwa halaman yang diminta tidak ditemukan.

3. **Refactoring Code**  
   - Refactoring dilakukan dengan memanfaatkan destructuring assignment untuk mengurangi duplikasi kode.  
   - Dengan membuat tuple `(status_line, filename)` berdasarkan validasi request, kode menjadi lebih maintainable.  
   - Perubahan ini membuat bagian yang membaca file dan menulis respons ke stream hanya ditulis satu kali, sehingga perubahan di masa depan dapat dilakukan lebih efisien.

4. **Keunggulan Refactor**  
   - Tujuan refactor adalah memudahkan pembacaan dan perawatan kode, serta meningkatkan readability logic yang membedakan antara request valid dan request tidak valid.  

Setelah menjalankan server dan mengakses `http://127.0.0.1:7878`, halaman `hello.html` tampil dengan benar. Sedangkan untuk request lainnya, seperti `http://127.0.0.1:7878/bad`, browser menampilkan halaman error yang berisi konten dari `404.html`.

![Commit 3 screen capture](/assets/images/commit3.png)

# Commit 4 Reflection

Pada milestone ini, saya menambahkan simulasi slow request pada web server untuk mengamati dampak penggunaan single-thread dalam menangani request. Beberapa poin pentingnya adalah:

1. **Simulasi Slow Request**  
   - Dengan menambahkan `thread::sleep(Duration::from_secs(10));` pada kondisi request `GET /sleep HTTP/1.1`, server sengaja menunda respon selama 10 detik.
   - Hal ini mensimulasikan kondisi di mana proses yang memerlukan waktu lama dapat menghambat penanganan request lain.

2. **Dampak pada Single-threaded Server**  
   - Karena server berjalan pada single-thread, ketika satu request tertunda, seluruh server tidak dapat memproses request lain hingga penundaan selesai.
   - Dengan membuka dua jendela browser, satu request yang mengakses `/sleep` akan mengakibatkan request lainnya (misalnya `GET / HTTP/1.1`) harus menunggu.
   - Simulasi ini memberikan gambaran nyata tentang keterbatasan server yang hanya menggunakan satu thread. Dalam skenario production dengan banyak pengguna, hal ini dapat menyebabkan performa dan pengalaman yang buruk.
