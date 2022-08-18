# 1. Introduction

Created: August 15, 2022 1:35 PM

Kalau kita bicara soal cloud, DevOps, dan teknologi, rasanya tidak akan luput dari yang namanya Linux. Dan yang perlu kita sadari bahwa cloud kebanyakan berisi Linux servers yang saling terhubung dengan network atau jaringan komputer.

Memiliki dasar linux, jaringan, dan scripting yang kuat merupakan salah satu “syarat penting” untuk memiliki karir di bidang DevOps. Pada section ini, kita akan membuat sebuah project yang akan mengkombinasikan 3 elemen diatas, untuk “pemanasan” sebelum kita memasuki topik-topik lainnya.

# Timeline

Kami menyarankan untuk mempelajari ketiga topik ini secara mendalam dengan menggunakan timeline sebagai berikut:

[Untitled](1%20Introduction%2042822773f28f4d6ebea3355c9ea31f58/Untitled%20Database%205d427cbca6cd4e6384b506ffd7773f52.csv)

Akan tetapi kami juga menyadari bahwa tempo belajar setiap orang akan berbeda-beda, maka dari itu, kamu bisa menyesuaikan pace belajarmu, namun dengan catatan jangan melebihi dari guideline waktu yang ditentukan, ya, karena masih banyak hal yang perlu kita pelajari kedepannya.

Untuk mempermudah kalian dalam menentukan objektif belajar, serta memastikan tidak ada yang terlewat, kami sediakan *mindmap* terkait apa saja yang semestinya kalian pelajari untuk memahami jaringan komputer yang nantinya akan digunakan di karir DevOps kalian.

[data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

![DO - Network.jpg](1%20Introduction%2042822773f28f4d6ebea3355c9ea31f58/DO_-_Network.jpg)

# Why do We learn This? (An Overview)

Setelah kita mempelajari dasar mengenai jaringan hingga topologi, nantinya kita akan coba untuk melihat  topologi yang akan kita bangun pada proses pembelajaran di sekolah devops cilsy. Topologi yang akan kita bangun merupakan gambaran akhir dari tujuan pembangunan infrastruktur cloud, dimana infrastruktur tersebut terdiri dari komponen yang ada pada AWS, Git, dan juga bagian local.Perlu dipahami, pada saat awal perancangan infrastruktur baiknya kita sudah merencanakan apa saja yang akan kita bangun terlebih dahulu. Agar memudahkan kita dalam membangun environment yang layak dan optimal untuk kebutuhan aplikasi kita.Berikut merupakan topologi yang akan kita gunakan pada proses pembelajaran ini :

****

[https://lh3.googleusercontent.com/FphHFAvCAtn0sGRhv1EKBrUZotGRIyqz9r0IV379RDXZzc4hc1U5kTh7Hg-MfKvvARB92Pj6wAZpQ3TpvNrkyhoMcyR9ppuWl0Lfkh3271vJGNRjD5gVkksPBndkznlQiFg32830qxhJPCF_3Q](https://lh3.googleusercontent.com/FphHFAvCAtn0sGRhv1EKBrUZotGRIyqz9r0IV379RDXZzc4hc1U5kTh7Hg-MfKvvARB92Pj6wAZpQ3TpvNrkyhoMcyR9ppuWl0Lfkh3271vJGNRjD5gVkksPBndkznlQiFg32830qxhJPCF_3Q)

[data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**Note**: Topologi diatas hanya berupa gambaran besarnya saja, Setiap orang berhak mengubah topologi tersebut berdasarkan tingkat pemahamannya, entah itu nantinya akan semakin besar, ataupun semakin simple, tergantung best practices yang dipahami.