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