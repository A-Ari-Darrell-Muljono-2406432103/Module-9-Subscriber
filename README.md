# Understanding Subscriber and Message Broker

**a. What is AMQP?**
AMQP (Advanced Message Queuing Protocol) adalah protokol standar terbuka di lapisan aplikasi (*application layer*) yang dirancang khusus untuk *message-oriented middleware*. Protokol ini beroperasi pada level *byte-stream* dan mendikte secara presisi bagaimana pesan dibungkus, dirutekan melalui *exchange*, diantrekan dalam *queue*, dan dikirimkan dengan jaminan koneksi (*reliability guarantees*) antar sistem yang saling terisolasi.

**b. Breakdown of `guest:guest@localhost:5672`**
URL ini adalah *Connection String* berbasis standar URI yang menstrukturkan parameter autentikasi dan *routing* TCP ke server RabbitMQ.
* **`guest` (pertama):** Kredensial *username default* sistem untuk otorisasi akses.
* **`guest` (kedua):** *Password default* yang divalidasi terhadap *username* tersebut.
* **`localhost`:** *Hostname* yang menunjuk ke *loopback interface* (127.0.0.1), menginstruksikan soket jaringan untuk mengeksekusi koneksi ke *broker* di mesin lokal.
* **`5672`:** *Port* TCP *default* tempat proses RabbitMQ melakukan *listening* untuk koneksi *inbound* berbasis AMQP.

PHOTO 1:
![Photo1](photo/photo1.png)

**Why the total number of queue is as such?**
Kuantitas akumulasi antrean merepresentasikan defisit antara beban trafik *publisher* dan *throughput* komputasi *subscriber*. Total 15 antrean mengindikasikan *publisher* dieksekusi 3 repetisi (3 *run* x 5 *event* = 15 *event*). Di sisi penerima, injeksi kode `thread::sleep(ten_millis)` memblokir *thread* pemrosesan sistem secara konstan selama 1 detik per eksekusi. RabbitMQ bertindak sebagai kompensator arsitektural dengan menahan (*buffering*) pesan tersebut di dalam *queue* untuk mencegah *data loss* atau *crash* akibat *subscriber* yang mengalami malfungsi kecepatan.

PHOTO 2:
![Photo2](photo/photo2.png)

**Reflection on Queue Reduction:**
Aktivasi tiga instans *subscriber* secara konkuren menekan laju akumulasi pesan secara drastis. Arsitektur pemrosesan ini mendemonstrasikan pola **Competing Consumers**. RabbitMQ mengeksekusi mekanisme algoritma *Round-Robin dispatching*, yang secara otomatis mendistribusikan muatan pesan secara bergilir kepada seluruh koneksi TCP *subscriber* yang terikat ke antrean *user_created*. Integrasi horizontal ini meningkatkan skala pemrosesan komputasi (*horizontal scaling*), mengubah resolusi operasional dari serial menjadi agregat paralel asinkron, sehingga *bottleneck* terselesaikan tanpa optimalisasi kode di sisi aplikasi pekerja tunggal.

**Code Improvement Suggestions:**
Berdasarkan tinjauan arsitektural, implementasi saat ini menggunakan parameter *hardcode* dan mekanisme eksekusi panik (*panic-on-error*) yang tidak valid untuk lingkungan tingkat produksi. Modifikasi perlu dilakukan di sisi inisialisasi koneksi.