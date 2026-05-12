# Understanding Subscriber and Message Broker

**a. What is AMQP?**
AMQP (Advanced Message Queuing Protocol) adalah protokol standar terbuka di lapisan aplikasi (*application layer*) yang dirancang khusus untuk *message-oriented middleware*. Protokol ini beroperasi pada level *byte-stream* dan mendikte secara presisi bagaimana pesan dibungkus, dirutekan melalui *exchange*, diantrekan dalam *queue*, dan dikirimkan dengan jaminan koneksi (*reliability guarantees*) antar sistem yang saling terisolasi.

**b. Breakdown of `guest:guest@localhost:5672`**
URL ini adalah *Connection String* berbasis standar URI yang menstrukturkan parameter autentikasi dan *routing* TCP ke server RabbitMQ.
* **`guest` (pertama):** Kredensial *username default* sistem untuk otorisasi akses.
* **`guest` (kedua):** *Password default* yang divalidasi terhadap *username* tersebut.
* **`localhost`:** *Hostname* yang menunjuk ke *loopback interface* (127.0.0.1), menginstruksikan soket jaringan untuk mengeksekusi koneksi ke *broker* di mesin lokal.
* **`5672`:** *Port* TCP *default* tempat proses RabbitMQ melakukan *listening* untuk koneksi *inbound* berbasis AMQP.