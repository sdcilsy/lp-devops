# 7. Setup Webhooks Jenkins

Created: July 21, 2022 2:36 PM

Kali ini kita akan membuat integrasi CI/CD menggunakan Github dan Jenkins menggunakan webhook. Secara konsep, kita akan membuat CI/CD pada suatu aplikasi Laravel dimana jika ada perubahan pada repository, maka Jenkins secara otomatis akan mem-build aplikasi tersebut kedalam image docker setelah itu menguploadnya ke [https://hub.docker.com](https://hub.docker.com/). Setelah itu melakukan deployment pada server menggunakan Docker dengan image yang sudah di upload tadi.

[https://lh4.googleusercontent.com/CW4Dajx8mSzbuEsA97r1Eh2tI-41JCXqjRkVGsA_Ak2923gY_zvJskjvtZ6Tdp5TCEaewQvmsMqy0pic36d8AGTw2qjr73ICFW0TLoRjnnkbrjrW1N3vwTRIto3smi6AfnKT83hdOwkRj9kjZZfMFg](https://lh4.googleusercontent.com/CW4Dajx8mSzbuEsA97r1Eh2tI-41JCXqjRkVGsA_Ak2923gY_zvJskjvtZ6Tdp5TCEaewQvmsMqy0pic36d8AGTw2qjr73ICFW0TLoRjnnkbrjrW1N3vwTRIto3smi6AfnKT83hdOwkRj9kjZZfMFg)

Adapun beberapa persyaratan pada praktikum ini adalah:

- Jenkins yang sudah terinstall pada AWS EC2, atau sebagainya yang dapat dijangkau oleh Github.
- Sudah terpasang Plugin Jenkins bernama **Github Plugin** (https://plugins.jenkins.io/github/).
- Docker sudah terinstall di server EC2.
- Account Docker (https://hub.docker.com/signup)
- Jika melihat account **profesorgreen36**, dapat diganti dengan account github atau docker masing masing.

# ***Setup* Github Repository**

Aplikasi yang akan kita coba deploy adalah aplikasi laravel yang sudah kita *push* ke *repository* github. Kalian dapat men-*clone* isi dari *repository* ini ke *repository* kalian yang baru sebagai sampel aplikasi laravel [https://github.com/adisaputra10/laravel.git](https://github.com/adisaputra10/laravel.git).

Kita akan melakukan **fork** terhadap repository tersebut. Caranya dengan mengeklik tombol fork yang ada pada repository [https://github.com/adisaputra10/laravel.git](https://github.com/adisaputra10/laravel.git).

[https://lh4.googleusercontent.com/x666PMBPEK25arCRzVlV9QVwj2cnOIU_PAcJk9IQrVmSBAFwGNymtF0XxzO_gfTqpe2-njZYFaqt6xV9LeArweoex9V2bGIOBucQNWkoFOXpIpdf9toMLjP-rBwOoWncseNwOf3O2J8ld3I2BUWcpw](https://lh4.googleusercontent.com/x666PMBPEK25arCRzVlV9QVwj2cnOIU_PAcJk9IQrVmSBAFwGNymtF0XxzO_gfTqpe2-njZYFaqt6xV9LeArweoex9V2bGIOBucQNWkoFOXpIpdf9toMLjP-rBwOoWncseNwOf3O2J8ld3I2BUWcpw)

Setelah itu, kita dapat mengganti nama repository kita. Ini optional. Hasilnya kurang lebih akan seperti ini.

[https://lh6.googleusercontent.com/JxBC_v6RwyGmPdBpF6bwtl4e3ujYsks1Sbqt-St9Ynefxs9qG0S5maSiTy_ZFmzptMhfiI3IZXgeG0aWRzJlG_SUdt02P-r9hssVfw_j8h9g67ZUh9vqZsnHpHSH2h5JVRoj8emL6-FFRH_lKO-UDQ](https://lh6.googleusercontent.com/JxBC_v6RwyGmPdBpF6bwtl4e3ujYsks1Sbqt-St9Ynefxs9qG0S5maSiTy_ZFmzptMhfiI3IZXgeG0aWRzJlG_SUdt02P-r9hssVfw_j8h9g67ZUh9vqZsnHpHSH2h5JVRoj8emL6-FFRH_lKO-UDQ)

# ***Setup Users***

Ada beberapa user yang akan kita gunakan untuk praktikum kali ini yaitu user jenkins, user ubuntu dan account docker.

Kita akses server EC2 dengan menggunakan SSH, lalu mengisikan user **ubuntu** dan **jenkins** kedalam group **docker.**

```bash
usermod -aG docker ubuntuusermod -aG docker jenkins
```

Agar dapat menerapkan perubahan, disarankan untuk **restart service jenkins**.

```bash
sudo systemctl restart jenkins
```

Lalu menggunakan user jenkins tersebut kita akan login docker menggunakan account docker masing masing.

```bash
sudo su jenkinsdocker login
```

Masukan *user* dan *password* dari akun docker hub yang kita miliki, apabila kita belum memiliki akun tersebut kita bisa buat akun terlebih dahulu di [https://hub.docker.com](https://hub.docker.com/).

# ***Setup* Github Webhooks**

Pada bagian ini kita akan coba melakukan ***setup* webhooks** pada **github** yang akan kita hubungkan pada *server* jenkins yang kita miliki. Kita akan coba melakukan *trigger*, dimana ketika kita melakukan push pada Github maka pada saat itu Jenkins tersebut akan otomatis melakukan *deploy* pada *server*.

Pertama buka Repository kita yang ada di github. Kemudian setelah itu klik tombol **Setting** yang ada disamping atas selanjutnya masuk kedalam menu **Webhooks** yang ada disamping kiri menu.

[https://lh6.googleusercontent.com/K2ztDpO29JFLfemJeoOYhjizR1mDHxjQcfTpQiO7BDUppdibDK5xW370oUfVPWISu02TU-m5CSOSuKd8RMzZnimya63LXwOdEoSBwlg5B26VV7rbQJ-TB6_g0PSTF_zSnISnLbw2-yH5_HHWRaW3SQ](https://lh6.googleusercontent.com/K2ztDpO29JFLfemJeoOYhjizR1mDHxjQcfTpQiO7BDUppdibDK5xW370oUfVPWISu02TU-m5CSOSuKd8RMzZnimya63LXwOdEoSBwlg5B26VV7rbQJ-TB6_g0PSTF_zSnISnLbw2-yH5_HHWRaW3SQ)

Selanjutnya klik tombol **Add webhook** yang ada disamping kanan atas untuk menambahkan alamat jenkins untuk menghubungkannya ke GitHub.

[https://lh4.googleusercontent.com/njmmjlpiolFN-9IYipCRKbRv4r6JrswkrOnogauNgL6CVz9EZXRDOkmztKYQVVboMAkrx8BOOYX_O4M6fzWMj95Qnrg0nwhGE5Zi-FSlqst9-GYLAavuFaEHHq_jEx-NfocjcjEBrq46v_frSqPZAA](https://lh4.googleusercontent.com/njmmjlpiolFN-9IYipCRKbRv4r6JrswkrOnogauNgL6CVz9EZXRDOkmztKYQVVboMAkrx8BOOYX_O4M6fzWMj95Qnrg0nwhGE5Zi-FSlqst9-GYLAavuFaEHHq_jEx-NfocjcjEBrq46v_frSqPZAA)

Kemudian tambahkan alamat URL dari jenkins yang kita miliki dengan menambahkan ***/github-webhook/*** diujung alamatnya seperti dibawah ini. Biarkan bagian **content type** dan **event trigger** secara *defaults*. Setelah itu simpan.

[*Url webhooks github*](https://lh6.googleusercontent.com/8-50zY4HsWvQyrNvjo3hyFvltlPKcxlI9fVLkafxvJP7pLjt63da_fL9P10-9Emusq9SiHeLeShJ0wKpdzFhHoNUKx5w7UMDFO5aOn9I76qY1RSns8wRwwnu3Wr-KIw4VQUl7DBVXI5f_ea4uOwc4Q)

*Url webhooks github*

# **Setup Webhook Jenkins**

Langkah selanjutnya kita akan melakukan konfigurasi pada **Jenkins**, maka kita akses dan *login* ke jenkins yang kita miliki. Setelah itu kita buat *item* baru.

Masukan nama *task* yang akan kita buat, misalkan disini kita buat dengan nama **jenkins_laravel**. Kemudian pilih ***freestyle*** sebagai itemnya, setelah itu klik **OK.**

[https://lh6.googleusercontent.com/y-Z6ysXp-DxgXnnQcTdIL-3vAuWEJ05kpDSu_dvBB_vqe7mEBDHeVHDuCDBNSSbub1fH1EpoUSOWqB0ydPRvRXfKOcK3H3P1V40t0_rvSN6hwoV-YcPMNP5YzSTtA5srhZ-b8qG_5ymiuhvnbbKsWQ](https://lh6.googleusercontent.com/y-Z6ysXp-DxgXnnQcTdIL-3vAuWEJ05kpDSu_dvBB_vqe7mEBDHeVHDuCDBNSSbub1fH1EpoUSOWqB0ydPRvRXfKOcK3H3P1V40t0_rvSN6hwoV-YcPMNP5YzSTtA5srhZ-b8qG_5ymiuhvnbbKsWQ)

Pada  bagian ***Source Code Management*,** pilih Git Kemudian copy URL dari *repository* aplikasi laravel yang kita miliki yaitu [https://github.com/adisaputra10/laravel.git](https://github.com/adisaputra10/laravel.git). Pilih juga ***Credentials*** yang akan kita gunakan. Jika belum membuat Credential, bisa dengan klik Add -> Jenkins lalu isikan username & password Github masing masing.

[https://lh6.googleusercontent.com/ijL6Xq-WHkm5hI8WZMVBrmO308GgEfUMuQjA_0KMQK6Km9Qic8A99U9bvbvcchbu-1QXaanqiQui1gO6Edykp50tDCp8tP9wY3z453fG1HwSY1hBDP4W6HIHQdCC_WSvFVU8WhwnMWckMjS2Nc3yzg](https://lh6.googleusercontent.com/ijL6Xq-WHkm5hI8WZMVBrmO308GgEfUMuQjA_0KMQK6Km9Qic8A99U9bvbvcchbu-1QXaanqiQui1gO6Edykp50tDCp8tP9wY3z453fG1HwSY1hBDP4W6HIHQdCC_WSvFVU8WhwnMWckMjS2Nc3yzg)

Pada bagian **Build Triggers**, centang option **GitHub hook trigger for GITScm polling** seperti gambar dibawah ini.

[https://lh6.googleusercontent.com/G-ymAQzrM6_n2E0qhg807BSmoUmqsSWCx_zC6Adf3ZXyJmTUbCx_RjX_qH3-UQ4Xg6p2wOvjeKKtoovrJsM4jzWeXUTXHfZFpnwWzXGwtvCS8DKF9A63xFOT2xamUtop5b9yhvGgGYU1Px4nqtOY6g](https://lh6.googleusercontent.com/G-ymAQzrM6_n2E0qhg807BSmoUmqsSWCx_zC6Adf3ZXyJmTUbCx_RjX_qH3-UQ4Xg6p2wOvjeKKtoovrJsM4jzWeXUTXHfZFpnwWzXGwtvCS8DKF9A63xFOT2xamUtop5b9yhvGgGYU1Px4nqtOY6g)

Scoll kebagian bawah, pada bagian Build klik **Add build step** lalu tambahkan **Execute shells** baru**.**

Selanjutnya masukan *script* untuk *build* aplikasi/docker seperti dibawah ini. Setelah build image docker ini, nantinya image tersebut akan di push ke hub.docker.com dan akan digunakan saat deploy aplikasi.

```bash
docker build . -t profesorgreen36/laravel:$BUILD_NUMBER
docker push profesorgreen36/laravel:$BUILD_NUMBER
```

[https://lh4.googleusercontent.com/LRWolUH736ebSpZFhDHQYigqVp4eh33h5E_Ft7tRKRu62JH3mA_HugVLprzpNavgal3f7knTe36p47MB2-P8QT2N_cFIGk4CmityvPZlLffjeavcyOaayRIlNgRVYimzQB8MMdG0pBv0WunRMWm0kg](https://lh4.googleusercontent.com/LRWolUH736ebSpZFhDHQYigqVp4eh33h5E_Ft7tRKRu62JH3mA_HugVLprzpNavgal3f7knTe36p47MB2-P8QT2N_cFIGk4CmityvPZlLffjeavcyOaayRIlNgRVYimzQB8MMdG0pBv0WunRMWm0kg)

Selanjutnya klik kembali tombol **Add build step**, setelah itu pilih **Execute Shell** untuk mendeploy image yang sudah di **build** tadi pada docker dengan command sebagai berikut.

```bash
docker pull profesorgreen36/laravel:$BUILD_NUMBER
docker rm -f laravel
docker run -dit --name laravel -p 80:8000 profesorgreen36/laravel:$BUILD_NUMBER
```

[https://lh6.googleusercontent.com/R353KlbDuy06ZeuOvY2A6v6Hwc6JVRndSRJKmFs2Z8a1SbtRei8AN9vPyRQX6KRhlWMRpJ5fPqrLu9JYAPkLxQhiHgPtjk_FrlalslZFlYYF8i3Bvq478qX2EaYQ-Yr2JsojDX_F7M8GG9WG0x75yQ](https://lh6.googleusercontent.com/R353KlbDuy06ZeuOvY2A6v6Hwc6JVRndSRJKmFs2Z8a1SbtRei8AN9vPyRQX6KRhlWMRpJ5fPqrLu9JYAPkLxQhiHgPtjk_FrlalslZFlYYF8i3Bvq478qX2EaYQ-Yr2JsojDX_F7M8GG9WG0x75yQ)

Setelah itu simpan *setting*-an yang sudah kita buat tersebut, maka akan muncul seperti dibawah ini hasilnya.

[https://lh5.googleusercontent.com/_TnyyVCxzrZGGXwBTsZ7vqbVQwsYGKPJG4Ge8X1tIX04ISOvY1w8Qn52wiu-Obj8sva1jhsE03VxSy6AEM3ADLfDBjNUXmHmidMqpBxkShETAxAEkqSIIVnyGvnN_RfROMGEnEIKN6a0AkYYzVvbYA](https://lh5.googleusercontent.com/_TnyyVCxzrZGGXwBTsZ7vqbVQwsYGKPJG4Ge8X1tIX04ISOvY1w8Qn52wiu-Obj8sva1jhsE03VxSy6AEM3ADLfDBjNUXmHmidMqpBxkShETAxAEkqSIIVnyGvnN_RfROMGEnEIKN6a0AkYYzVvbYA)

Selanjutnya kita coba **mengubah code di repository** dan melakukan **push** pada **GitHub**. Contohnya dengan mengubah file **README.md** yang ada pada repository.

Maka dengan otomatis github akan men-*trigger* ke jenkins, sehingga jenkins akan melakukan *build* dengan sedirinya seperti dibawah ini.

[https://lh4.googleusercontent.com/9mEt_cc1NhqjGzzWFdbUJHkxO_BNrYd171Y5lDscDm8NYesdYl8mxw6O5-bYqWmhSXLI5rAq4RzkF9fM1Yttk91QmhHBXQIotq11urW34rvRUK9EyH6EtbrgKYJzfc16alGS25MQqHoKac851cjWPg](https://lh4.googleusercontent.com/9mEt_cc1NhqjGzzWFdbUJHkxO_BNrYd171Y5lDscDm8NYesdYl8mxw6O5-bYqWmhSXLI5rAq4RzkF9fM1Yttk91QmhHBXQIotq11urW34rvRUK9EyH6EtbrgKYJzfc16alGS25MQqHoKac851cjWPg)

Jika kita ingin melihat log secara detail, kita hanya perlu klik salah satu nomor di atas, kemudian klik menu **console output** disamping kiri, dan apabila *deployment* berhasil maka hasilnya akan seperti di bawah ini.

[https://lh3.googleusercontent.com/QZbA1VTnHz4-2Ks9nbQ2LyTCec7aLTi5WpqN2AWXbVR1E9m1ARdMyzeyjLVtbxrL0dXjF1OwkoLcMvH1BcQKwqmC0rwbmkoXkmCKOpjr8FqMwZ_Ozu0flKuPrq9Fn5H7RySl0rzWaRM1ug5nJ_rw9w](https://lh3.googleusercontent.com/QZbA1VTnHz4-2Ks9nbQ2LyTCec7aLTi5WpqN2AWXbVR1E9m1ARdMyzeyjLVtbxrL0dXjF1OwkoLcMvH1BcQKwqmC0rwbmkoXkmCKOpjr8FqMwZ_Ozu0flKuPrq9Fn5H7RySl0rzWaRM1ug5nJ_rw9w)

Setelah berhasil kita lakukan *deploy*, coba kita akses *server* tujuan yang kita *deploy* aplikasi laravel tersebut dengan menggunakan *port* 80. seperti **IPSERVER:80**. Jika berhasil, maka hasilnya akan seperti diabwah ini.

[*Hasil simple web laravel dengan webhooks*](https://lh4.googleusercontent.com/RNodispmjsblfjsOm46FsrRQ2sQbMcBujSXiyjEeQcCVojcleVaVyK0lcKaWLK9pMvCx1x_nWmmBIgzsPw7vZ4PHvsqx9nIAQefW28D8XyR1uscwQv590S7nLhZUujBiucliBSu4EX7Tjif4Dt_zkg)

*Hasil simple web laravel dengan webhooks*

# **Setup POL SCM dengan aplikasi Golang (Iris)**

Selain daripada **webhook**, jenkins sendiri memiliki **POL SCM** yang dapat dugunakan sebagai trigger. Perbedaan webhook dan POL SCM adalah sumber *trigger*, jika kita menggunakan POL SCM maka trigger dari jenkins akan diatur dengan waktu tertentu untuk membaca repository jika ada perubahaan. Sedangkan webhook melakukan trigger berdasarakan push dari para *programmer*.

Berikut merupakan langkah untuk mengatur **POL SCM** di jenkins, pertama kita buat sebuah item baru pada jenkins dengan **Freestyle Project**.

[https://lh4.googleusercontent.com/Pu444MAAHOGy6K024bfK3DcSIsOXWdeIsFv4PsvrZZzjXCXl5Exy99uuHsgnKuj9pIHHCK_a-Je3yqhjnAbCM1DjVDg32JLUE3bU3gSXdZsCzHb7vkfp_r3gAdiU-CynDGuNzeR20aAjro7BOKhD5A](https://lh4.googleusercontent.com/Pu444MAAHOGy6K024bfK3DcSIsOXWdeIsFv4PsvrZZzjXCXl5Exy99uuHsgnKuj9pIHHCK_a-Je3yqhjnAbCM1DjVDg32JLUE3bU3gSXdZsCzHb7vkfp_r3gAdiU-CynDGuNzeR20aAjro7BOKhD5A)

Selanjutnya pilih Git pada bagian **Source Code Management,** isikan **Repository URL** dengan alamat [https://github.com/adisaputra10/golang.git](https://github.com/adisaputra10/golang.git). Pilih juga **Credentials** dengan yang sudah kita buat sebelumnya.

[https://lh3.googleusercontent.com/_tdcv78ZRdOna84A9TAh0p4xoqny1qyjhZ6Kjms19aAV7J1KsePf9fifo34ToZNv4aPCwKpWpZy0ZOqjacLqvS3CEbpC3cjHJ9VE_xB6yj3ItorGI2vB1n_iP5MAKoLPpBWJksrmCq1Mb3zw85GBsg](https://lh3.googleusercontent.com/_tdcv78ZRdOna84A9TAh0p4xoqny1qyjhZ6Kjms19aAV7J1KsePf9fifo34ToZNv4aPCwKpWpZy0ZOqjacLqvS3CEbpC3cjHJ9VE_xB6yj3ItorGI2vB1n_iP5MAKoLPpBWJksrmCq1Mb3zw85GBsg)

Pada bagian Build *Triggers*, centang **Poll SCM** kemudian isi dengan ( * * * * * ).

[https://lh3.googleusercontent.com/a5NrbGxVTTionWphup9p9rUrRrI1qOSLk7Of-3TfTLlINibcYo2Eaoaj0nqg0DshvoNZqMY3gv2GOAiSs4MjOxDBgZNqeXh49QGZ3eU1gU7i4tc4u0CeMbbMzCZCDK4kg4PrNmurMxzPSRYdksn1kw](https://lh3.googleusercontent.com/a5NrbGxVTTionWphup9p9rUrRrI1qOSLk7Of-3TfTLlINibcYo2Eaoaj0nqg0DshvoNZqMY3gv2GOAiSs4MjOxDBgZNqeXh49QGZ3eU1gU7i4tc4u0CeMbbMzCZCDK4kg4PrNmurMxzPSRYdksn1kw)

Pada bagian *Build*, Tambahkan **Execute Shell** dengan perintah dibawah ini.

```bash
docker build . -t adisaputra10/golang:$BUILD_NUMBER
docker push adisaputra10/golang:$BUILD_NUMBER
```

Maka hasilnya akan muncul seperti dibawah ini.

[https://lh5.googleusercontent.com/8y3odeWb9BRqKORKrWomdQmoffBmRwo4AN4tkoGkWU1fnvgNK7zUyrzftLwAh2PiQ_vn_5FGrzczQ4sIwbSooypL80DRHo6mH8vQCHb5jQtDH6z7Tpj0ZQ4UZ_Hh5G5r3q_MGs3PlgDDK9Wms_q8iQ](https://lh5.googleusercontent.com/8y3odeWb9BRqKORKrWomdQmoffBmRwo4AN4tkoGkWU1fnvgNK7zUyrzftLwAh2PiQ_vn_5FGrzczQ4sIwbSooypL80DRHo6mH8vQCHb5jQtDH6z7Tpj0ZQ4UZ_Hh5G5r3q_MGs3PlgDDK9Wms_q8iQ)

Selanjutnya klik kembali tombol **Add build step**, setelah itu tambahkan **Send files or execute commands over SSH**. Dan pada bagia exec commands, isikan script dibawah ini.

```bash
docker pull  adisaputra10/golang:$BUILD_NUMBER;
docker rm -f golang
docker run -dit -p 80:80 --name  golang adisaputra10/golang:$BUILD_NUMBER
```

Maka hasilnya bisa kita sesuaikan seperti gambar dibawah ini.

[https://lh3.googleusercontent.com/6e2H-IjiyEov8Wq7xFAeQRYnDIF3Z5SvJMtOKboUpdIFy-dScEJTz3VJ8_wyrPBRVuxemYZj9EQ8tndFbiuqgwYkDd7iQIYlk8_xhuo4sTYGQmhehTeHOWJgQGYeMoC92QhZAF-uEZ_QNgRPSI1EGg](https://lh3.googleusercontent.com/6e2H-IjiyEov8Wq7xFAeQRYnDIF3Z5SvJMtOKboUpdIFy-dScEJTz3VJ8_wyrPBRVuxemYZj9EQ8tndFbiuqgwYkDd7iQIYlk8_xhuo4sTYGQmhehTeHOWJgQGYeMoC92QhZAF-uEZ_QNgRPSI1EGg)

Kemudian simpan konfigurasi yang sudah kita buat. Maka hasilnya akan menjadi seperti dibawah ini.

[https://lh3.googleusercontent.com/_RZZwMIBgDmS0y89Ug0FsFRv4OcU-gU0dAohS6BzHBSRLHhmmbb0_1-gO7An-Y-vaTk_SSkhWyFwfdrqw9h5fP_kBvswadpz8xnVxiA11nCStv7MHaza385uzBOKFILIvJ1Ol_ZB5EKUu1W6wCGDKQ](https://lh3.googleusercontent.com/_RZZwMIBgDmS0y89Ug0FsFRv4OcU-gU0dAohS6BzHBSRLHhmmbb0_1-gO7An-Y-vaTk_SSkhWyFwfdrqw9h5fP_kBvswadpz8xnVxiA11nCStv7MHaza385uzBOKFILIvJ1Ol_ZB5EKUu1W6wCGDKQ)

Selanjutnya kita coba mengubah *code* di *repository* dan melakukan *push* pada GitHub. Maka dengan otomatis github akan men-*trigger* ke jenkins, sehingga jenkins akan melakukan *build* dengan sedirinya seperti dibawah ini.

[https://lh4.googleusercontent.com/9mEt_cc1NhqjGzzWFdbUJHkxO_BNrYd171Y5lDscDm8NYesdYl8mxw6O5-bYqWmhSXLI5rAq4RzkF9fM1Yttk91QmhHBXQIotq11urW34rvRUK9EyH6EtbrgKYJzfc16alGS25MQqHoKac851cjWPg](https://lh4.googleusercontent.com/9mEt_cc1NhqjGzzWFdbUJHkxO_BNrYd171Y5lDscDm8NYesdYl8mxw6O5-bYqWmhSXLI5rAq4RzkF9fM1Yttk91QmhHBXQIotq11urW34rvRUK9EyH6EtbrgKYJzfc16alGS25MQqHoKac851cjWPg)

Jika ingin melihat detail dari *log build history*, kita bisa klik salah satu nomer yang ada diatas.