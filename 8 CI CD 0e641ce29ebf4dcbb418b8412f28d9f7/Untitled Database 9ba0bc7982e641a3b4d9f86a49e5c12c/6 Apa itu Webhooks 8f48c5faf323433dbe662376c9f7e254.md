# 6. Apa itu Webhooks ?

Created: July 21, 2022 2:35 PM

**Webhooks** adalah layanan yang memungkinkan kita membuat atau mengatur aplikasi GitHub yang meng-*subscribe* ke *event* tertentu di GitHub.com. Ketika salah satu dari *event* tersebut di *trigger*, maka akan mengirim payload HTTP POST ke URL konfigurasi webhook. Webhook dapat digunakan untuk memicu pembuatan CI, memperbarui *backup mirror*, atau bahkan men-*deploy* ke *server* *production*.

Webhooks dapat dipasang pada repositori tertentu. Setelah dipasang, webhook akan men-*trigger* setiap kali satu atau beberapa *event* dari *subscriber* terjadi. Kita dapat membuat hingga 20 webhook untuk setiap *event* pada setiap target pemasangan (organisasi spesifik atau repositori khusus).

Dengan menggunakan layanan webhooks ini kita bisa menghubungkan GitHub dengan Jenkins sehingga ketika kita melakukan *push program* ke GitHub, maka pada saat itu juga jenkins akan ter-

*trigger* dan melakukan *deploy* pada *server* yang kita sudah set sebelumnya.