## Commit 1 Reflection

Dalam milestone ini, saya mengimplementasikan web server single-threaded sederhana menggunakan Rust. Tujuannya adalah untuk memahami bagaimana server mendengarkan koneksi TCP yang masuk, menerimanya, dan memproses permintaan HTTP dari browser.

Rincian Implementasi: 
- Menyiapkan TCP Listener:
Saya menggunakan TcpListener::bind("127.0.0.1:7878") untuk membuat listener yang menunggu koneksi pada alamat dan port yang ditentukan.

- Menangani Koneksi Masuk:
Program melakukan iterasi pada koneksi yang masuk dengan menggunakan for stream in listener.incoming(), sehingga setiap permintaan koneksi diterima dan diproses.

- Memproses Request HTTP:
Saya menambahkan fungsi handle_connection. Di dalam fungsi ini, Sebuah BufReader membungkus TcpStream yang masuk untuk membaca permintaan baris per baris. Lalu, Request HTTP dikumpulkan ke dalam vektor string dengan membaca hingga ditemukan baris kosong. Metode ini secara efisien mengumpulkan semua header HTTP yang dikirim oleh browser. Terakhir, Request tersebut kemudian dicetak ke console untuk keperluan debugging.


Saya menyadari bahwa browser terkadang melakukan beberapa koneksi untuk URL yang sama, yang mengakibatkan munculnya  pesan "Connection established!" di console. Hal ini wajar, karena browser sering mencoba ulang permintaan jika respons tidak diterima dengan cepat.

Singkatnya, Latihan ini memperdalam pemahaman saya tentang koneksi TCP dan bagaimana permintaan HTTP disusun dan dikirim melalui koneksi tersebut. Kemudian, penggunaan standard library Rust (khususnya modul std::net dan std::io) memungkinkan saya membuat web server yang sederhana namun efektif. Selain itu, Mencetak permintaan HTTP ke konsol membantu saya memastikan bahwa server berhasil membaca dan menangani koneksi yang masuk.

## Commit 2 Reflection

Pada milestone ini, saya mengembangkan web server sederhana agar dapat mengembalikan halaman HTML sebagai respons terhadap permintaan HTTP dari browser. Beberapa poin penting yang saya pelajari dari perubahan kode pada fungsi `handle_connection` adalah sebagai berikut:

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

4. **Memahami Alur Permintaan HTTP**  
   - Kode membaca permintaan HTTP menggunakan `BufReader` dan mengumpulkan setiap baris hingga menemukan baris kosong.  
   - Hal ini memungkinkan pemahaman mengenai bagaimana browser mengirimkan header HTTP dan kapan koneksi seharusnya berhenti membaca.

5. **Pentingnya Lokasi File**  
   - File `hello.html` harus berada di direktori yang sama dengan lokasi eksekusi program agar dapat dibaca dengan benar.  
   - Jika file tidak ditemukan, program akan berhenti dengan error karena penggunaan `.unwrap()`.


Setelah menjalankan `cargo run` dan mengakses `http://127.0.0.1:7878` melalui browser, halaman HTML berhasil ditampilkan. Berikut adalah tangkapan layar dari tampilan browser:

![Commit 2 screen capture](/assets/images/commit2.png)