# 8. Deploy On Remote Server

Created: July 21, 2022 2:40 PM

Pada praktikum sebelumnya kita melakukan build, test, dan deploy pada server local. Kali ini berbeda kita akan mencoba melakukan deploy apps pada remote server. Dengan menggunakan aplikasi [https://github.com/sdcilsy/todo-react](https://github.com/sdcilsy/todo-react), kita akan membuat automation deployment pada vagrant machine yaitu Ubuntu 18 dengan web server Apache 2 .

Requirement:

- Forked [https://github.com/sdcilsy/todo-react](https://github.com/sdcilsy/todo-react).
- NodeJS 16.x (LTS)
- Jenkins 2.319.1
- Jenkins Plugin “Publish Over SSH”
- Vagrant

[https://lh4.googleusercontent.com/1hikMZ8wmYljKIJ_a-3x_lm8jCCdgtsVBM6PHETNAOed68JofVnnj4qFAjo39Yalt12WmfkiesR2s9Ix4DLMfogWJa4XYh7Hine2j8M94a2FC3wfFifxF0tyelo4lmxW_5ZXQGTg2ijxcj0hhbgxwg](https://lh4.googleusercontent.com/1hikMZ8wmYljKIJ_a-3x_lm8jCCdgtsVBM6PHETNAOed68JofVnnj4qFAjo39Yalt12WmfkiesR2s9Ix4DLMfogWJa4XYh7Hine2j8M94a2FC3wfFifxF0tyelo4lmxW_5ZXQGTg2ijxcj0hhbgxwg)

Kurang lebih, begini flow CICD yang akan kita bangun kali ini. Kita akan membangun CICD pada pada environment local alias laptop.

1. User melakukan push ke repository
2. Webhook mentrigger build pada Jenkins
3. Jenkins melakukan build, test.
4. Deployment akan dilakukan pada vagrant machine dengan web server Apache2.

# **Setup Repository**

Seperti yang sudah disebutkan diawal, kita akan menggunakan aplikasi todo yang dibangun menggunakan reactjs. Repository ada pada [https://github.com/sdcilsy/todo-react](https://github.com/sdcilsy/todo-react) Teman teman bisa **fork** repository tersebut untuk praktikum ini.

[https://lh5.googleusercontent.com/mbjwJwbyTTYLTql8LhN7qdRbfW6vrg3gjCKeQCxxggcbjj7NvSUtfqSGg6dH_-06vEBJuUbl69KUgTbe30VlZMbCAy5Ztzq6cfUkvuo8M3CDljXKIY1aJr4g3muFPhoXwNkkSNtdQ2rHBEKGPZseLQ](https://lh5.googleusercontent.com/mbjwJwbyTTYLTql8LhN7qdRbfW6vrg3gjCKeQCxxggcbjj7NvSUtfqSGg6dH_-06vEBJuUbl69KUgTbe30VlZMbCAy5Ztzq6cfUkvuo8M3CDljXKIY1aJr4g3muFPhoXwNkkSNtdQ2rHBEKGPZseLQ)

# **Setup Ngrok**

Kita tahu bahwa public network tidak dapat mengakses private network. Github yang kita gunakan berada di public network sedangkan Jenkins berada pada private network. Lalu bagaimana dengan webhook dari Github ke Jenkins? Oleh karena itu, kita akan menggunakan **ngrok**, aplikasi tunnel yang memungkinkan service pada private network dapat diakses secara public.

Untuk menginstalnya, kita cukup menggunakan perintah dibawah.

```bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null &&
              echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list &&
              sudo apt update && sudo apt install ngrok
```

Setelah itu kita pastikan ngrok sudah terinstal dengan baik dengan perintah berikut.

```bash
ngrok -v
```

[https://lh6.googleusercontent.com/jxw0AveCh5bFz9WX5e9SZM_yZWIGIhGHo6mDhlMMjwKsPqd9xISH_S0nLTW7UxgaMi6zswwmX_2HcMkO11Il6vMYU40oZHHU_AEm5U-6vjE1We89zEtSIsC8bq7lArwaaoWe7N5_FOYYw6CbQZrkNQ](https://lh6.googleusercontent.com/jxw0AveCh5bFz9WX5e9SZM_yZWIGIhGHo6mDhlMMjwKsPqd9xISH_S0nLTW7UxgaMi6zswwmX_2HcMkO11Il6vMYU40oZHHU_AEm5U-6vjE1We89zEtSIsC8bq7lArwaaoWe7N5_FOYYw6CbQZrkNQ)

Setelah itu kita akan ekspos service Jenkins yang berjalan pada port 8080 menggunakan protocol http dengan perintah berikut.

```bash
ngrok http 8080
```

Setelah itu, akan ada informasi tunnel yang sudah kita buat. Ada informasi URL service Jenkins agar public bisa mengakses. Biarkan terminal ini berjalan.

[https://lh5.googleusercontent.com/H1opyW-yd6gxhuuz0tZb2UuXsOxGEB6wDhqSEfLxv6RH5Es31EWyoDwP3rDwPhrcTGSiN7I1yf8E3pT18NH8fladbSci0ugnXVsZ3URXI0zxaXblgLcQ-QDeZQwIGjATUdcbioXBWpq3RW8zYYWEYQ](https://lh5.googleusercontent.com/H1opyW-yd6gxhuuz0tZb2UuXsOxGEB6wDhqSEfLxv6RH5Es31EWyoDwP3rDwPhrcTGSiN7I1yf8E3pT18NH8fladbSci0ugnXVsZ3URXI0zxaXblgLcQ-QDeZQwIGjATUdcbioXBWpq3RW8zYYWEYQ)

# **Setup Webhook**

Apakah teman teman masih ingat materi webhook diatas? Kalau masih ingat, bagus karena kita akan memasang webhook pada pipeline ini. Untuk mensetting webhook, pada repository kita menuju **setting > webhook**.

Dan pada Payload URL, kita masukan URL yang tertera pada ngrok tadi. Kita copy yang menggunakan protocol HTTP. Dalam case saya, URL nya http://4300-125-164-23-241.ngrok.io. Dan jangan lupa diakhir URL tambahkan **/github-webhook/**.

[https://lh3.googleusercontent.com/mHN-ppYVVgjPtYjJNtPWEw0YE0wvOsnCAKjPdbpGgPMIzxasyXcRMjFKR6CtDBb7HF_HBCjUusqwtNHTulEIB5E6R6VcK3tHFFDfhuphkVM3F9QTGeXJU_JAaT_BvRU43gzKuDjY4SejuZvVmTeBgQ](https://lh3.googleusercontent.com/mHN-ppYVVgjPtYjJNtPWEw0YE0wvOsnCAKjPdbpGgPMIzxasyXcRMjFKR6CtDBb7HF_HBCjUusqwtNHTulEIB5E6R6VcK3tHFFDfhuphkVM3F9QTGeXJU_JAaT_BvRU43gzKuDjY4SejuZvVmTeBgQ)

### **Create Remote Server**

Masih ingat dengan Vagrant? Itu loh materi pada awal pembelajaran. Kalau lupa, silahkan baca kembali **section Linux Operating System**.

Dengan menggunakan Vagrant, kita akan membuat machine dengan OS Ubuntu 18 yang nantinya kita gunakan sebagai remote server untuk deployment aplikasi.

Untuk membuatnya kita cukup membuat inisiasi Vagrantfile dengan perintah berikut.

```bash
vagrant init ubuntu/bionic64
```

[https://lh4.googleusercontent.com/YTmHg7RHSX0dDSMmYvoA9zSxKamp0eugmvRbp6btq6EPA0gOSBEoY_DrNp9rA8EIR5Ewjgm-i9WDw2Sv9zlS1iBC11mH4QGIXQjxavSp5_0En6jVA-k5CkhT-W6t-eXuTf5ZEhhTTnQ4RpaM6bdDsA](https://lh4.googleusercontent.com/YTmHg7RHSX0dDSMmYvoA9zSxKamp0eugmvRbp6btq6EPA0gOSBEoY_DrNp9rA8EIR5Ewjgm-i9WDw2Sv9zlS1iBC11mH4QGIXQjxavSp5_0En6jVA-k5CkhT-W6t-eXuTf5ZEhhTTnQ4RpaM6bdDsA)

Setelah Vagrantfile terbuat, kita akan mengkonfigurasi machine agar satu network dengan kita dengan menggunakan interface bridge. Kita ubah Vagrantfile dan menambahkan interface bridge seperti ini.

```bash
nano Vagrantfile
```

[https://lh3.googleusercontent.com/cNGC0LCXOePQJM5oFduJzd82ygW43yeXjz5ckj13FcRHSLcxGRwLsi-DYalz_U3rtL4qCrEp82eGh6wLI2tggkM-ZZiwyg9QHKrOcUcoMRimQ5tCh55yMlgbDs2hz08iNUKdZhcLpbJiPxO30rBMJw](https://lh3.googleusercontent.com/cNGC0LCXOePQJM5oFduJzd82ygW43yeXjz5ckj13FcRHSLcxGRwLsi-DYalz_U3rtL4qCrEp82eGh6wLI2tggkM-ZZiwyg9QHKrOcUcoMRimQ5tCh55yMlgbDs2hz08iNUKdZhcLpbJiPxO30rBMJw)

Jika teman teman tidak tahu interface apa yang akan dipakai sebagai bridge, teman teman bisa cek list interface yang ada menggunakan perintah berikut.

```bash
ip a
```

Dalam case saya, saya memilih interface **Wifi (wlp9s0b1)** karena nantinya machine akan otomatis mendapat IP DHCP. Teman teman juga dapat menggunakan interface Wifi. Biasanya nama interface nya diawali dengan huruf **w**.

[https://lh4.googleusercontent.com/qTYmrrBLhCzxKKhYwJ5MqGvODOXouwMxd8X2M8SCJn0eq5dCa_bQQkJXcwU-AvYCFdo6skpdxWnR89n4d7QOoyLGtzi4gXljHaT7o0iOzcFH5QW592WHmhwGxYJFy6kTwXMClty-oPiIcX7f4EcxPQ](https://lh4.googleusercontent.com/qTYmrrBLhCzxKKhYwJ5MqGvODOXouwMxd8X2M8SCJn0eq5dCa_bQQkJXcwU-AvYCFdo6skpdxWnR89n4d7QOoyLGtzi4gXljHaT7o0iOzcFH5QW592WHmhwGxYJFy6kTwXMClty-oPiIcX7f4EcxPQ)

Lalu setelah itu kita jalankan machine dengan perintah **vagrant up**.

```bash
vagrant up
```

[https://lh4.googleusercontent.com/ddCPjckNHI45HxGLlc-qHw0FlKSfG-M9rCCgwOoQtngM4n3xStpbgywq3o7tPTTZ6eq8l71v6LyiHmjZBjkbNhBrNZk4HrkOmzEjjRLYYVrMZlWMN61oUc4FrD9Y36gV1Adqk6p2Nhm5-VhN6s1Oag](https://lh4.googleusercontent.com/ddCPjckNHI45HxGLlc-qHw0FlKSfG-M9rCCgwOoQtngM4n3xStpbgywq3o7tPTTZ6eq8l71v6LyiHmjZBjkbNhBrNZk4HrkOmzEjjRLYYVrMZlWMN61oUc4FrD9Y36gV1Adqk6p2Nhm5-VhN6s1Oag)

Selanjutnya kita akan menginstal web server untuk deployment aplikasi. Seperti yang disebutkan di awal, kita akan menggunakan Apache 2 sebagai web servernya.

```bash
sudo apt updatesudo apt install apache2 -y
```

Setelah web server terinstal, kita akan mengkonfigurasinya. Buka file /etc/apache2/apache2.conf dan ganti **AllowOverride None** menjadi **AllowOverride All**.

[https://lh3.googleusercontent.com/cftwzV_rPhOdwouDt2T1jvj5zfHAmHLQSaY7y2hwm4gLk2Zd_qkCpxeBNp6pcDTWkZ0oqD1LHMssZ-52iSycapSAZ3l6r137FLaHYoNpqHsgHEavf-Ywq4okcrs9oGvYt6GqInHXvN4uIhPm7_xzEw](https://lh3.googleusercontent.com/cftwzV_rPhOdwouDt2T1jvj5zfHAmHLQSaY7y2hwm4gLk2Zd_qkCpxeBNp6pcDTWkZ0oqD1LHMssZ-52iSycapSAZ3l6r137FLaHYoNpqHsgHEavf-Ywq4okcrs9oGvYt6GqInHXvN4uIhPm7_xzEw)

Ini diperlukan karena aplikasi yang sudah di build tadi membutuhkan directory khusus untuk bekerja. Karena nantinya aplikasi yang kita gunakan mengakses file javascript dan css dari folder khusus ini.

Setelah itu, kita menjalankan module apache2 bernama **rewrite**. Module ini yang nantinya bertanggungjawab agar aplikasi dapat mengakses directory khusus tersebut.

```bash
a2enmod rewrite
```

Setelah itu kita tinggal merestart service apache2.

```bash
sudo systemctl restart apache2
```

# **Manage Jenkins Plugin**

Jenkins memiliki banyak plugin yang dapat digunakan untuk mempermudah pembuatan pipeline. Salah satunya adalah **Publish Over SSH**. Plugin ini berguna untuk mengirimkan file/artifact menggunakan ssh yang cocok untuk kita gunakan pada praktikum ini, mengingat kita akan mendeploy aplikasi pada remote server.

Untuk menginstal plugin, kita tinggal menuju **manage jenkins** > **manage plugins**.

[https://lh5.googleusercontent.com/h2LFsRrcZgbfXrJDP3jw0S9vTbZ7vfPq676DLKfJsTjelI-zIuPcJ84_Rm87Oyc80t3fl-c2aMM3-emXJjsVYbyDihkveYTSDAElMrMC6PdX9tKD5hd_fHJ_sNjF1PQAzcUO9BnWsuH2VN60lhLyuw](https://lh5.googleusercontent.com/h2LFsRrcZgbfXrJDP3jw0S9vTbZ7vfPq676DLKfJsTjelI-zIuPcJ84_Rm87Oyc80t3fl-c2aMM3-emXJjsVYbyDihkveYTSDAElMrMC6PdX9tKD5hd_fHJ_sNjF1PQAzcUO9BnWsuH2VN60lhLyuw)

Kita bisa mencari plugin dengan mengetikan Publish Over SSH pada tab **available**. Klik **Download now and install after restart**.

[https://lh3.googleusercontent.com/sf63-_sWTc_9DUkD0rF85DC-IuTO5xFoqlBi-wgdnYllnyb2Fml0N_uyTOcdkVdpzUhKcJ8tb3ZrZsABJ_kQqphFRIi_wGj2ohDgcqcet0t94UcDazHo2bbK7IbwVChmE8KTJ6M4YismDtKpegPk7w](https://lh3.googleusercontent.com/sf63-_sWTc_9DUkD0rF85DC-IuTO5xFoqlBi-wgdnYllnyb2Fml0N_uyTOcdkVdpzUhKcJ8tb3ZrZsABJ_kQqphFRIi_wGj2ohDgcqcet0t94UcDazHo2bbK7IbwVChmE8KTJ6M4YismDtKpegPk7w)

Setelah plugin terinstall, langkah selanjutnya kita akan menambahkan remote server agar Jenkins dapat berkomunikasi dengan remote server.

Kita menuju **Manage Jenkins** > **Configure System.**

[https://lh3.googleusercontent.com/uHeJEFTQ9lQ7Btw4j3x-4yb4ZYa7ZKfI8KHQVrSzyOKBgtqbIq94Yx7s3c1XrcAdb2zVe7mLyml3Z7LU0mIUT43D9o7jkgGMGvvSEe56qenwBLJnhPKuqaK_8L39dNfyWwn-bT_X4BDYIJpFSll0FQ](https://lh3.googleusercontent.com/uHeJEFTQ9lQ7Btw4j3x-4yb4ZYa7ZKfI8KHQVrSzyOKBgtqbIq94Yx7s3c1XrcAdb2zVe7mLyml3Z7LU0mIUT43D9o7jkgGMGvvSEe56qenwBLJnhPKuqaK_8L39dNfyWwn-bT_X4BDYIJpFSll0FQ)

Scroll kebawah sampai menemukan setting **Publish over SSH**. Setelah itu ada beberapa informasi yang diisi yaitu Key. Key ini merupakan private key yang digunakan agar dapat mengakses remote server.

[https://lh6.googleusercontent.com/n7uxnBodAmEGIxk0jDQl-TK-lwaVV_SEVuc51B5-lnze2zG_ViiTtbdwR15saO2u5j1xfIlS4jcqGVOQ0nJPQjobnxqjtB2ZhZJMeoPAhabgsPsKVlMbSPwatZkDLTe1cseIgU4Mzj4V3qs9ThPk7A](https://lh6.googleusercontent.com/n7uxnBodAmEGIxk0jDQl-TK-lwaVV_SEVuc51B5-lnze2zG_ViiTtbdwR15saO2u5j1xfIlS4jcqGVOQ0nJPQjobnxqjtB2ZhZJMeoPAhabgsPsKVlMbSPwatZkDLTe1cseIgU4Mzj4V3qs9ThPk7A)

Karena kita membuat remote server dengan Vagrant, maka kita dapat mengambil private keynya pada directory dimana **Vagrantfile** berada.

```bash
cat .vagrant/machines/default/virtualbox/private_key
```

[https://lh6.googleusercontent.com/D90BcHDTztvnqo3-Ts14XMbLzQoTJKK14S_o5K2WX8uU3hEZz8IiURwLgyPBoJtjiiKBGpHgWTANr6lnZ20Mi8Nhz59k-dsBVVSeAzzt-zgvW3yILfha44In_CXKAjni9oHWXLWRkp6AfKTTFOC0XA](https://lh6.googleusercontent.com/D90BcHDTztvnqo3-Ts14XMbLzQoTJKK14S_o5K2WX8uU3hEZz8IiURwLgyPBoJtjiiKBGpHgWTANr6lnZ20Mi8Nhz59k-dsBVVSeAzzt-zgvW3yILfha44In_CXKAjni9oHWXLWRkp6AfKTTFOC0XA)

Copy kan isi key tersebut ke field key yang ada diatas.

Selanjutnya kita akan menambah server yang digunakan Jenkins untuk mengirim file atau artefact dengan mengklik **Add Server**.

[https://lh4.googleusercontent.com/XH6LZmDHeUyisk4C3gwsiLwXsc999TWhmsLfUqJL278as5Fg0oPs1JJuFdDUDe3fGUBN6fCSI5G0G8b3K5lLaiVCEH0rC5aHmw99WsfFWqOLFqWOxhKgOOmPVAh7e5qsgEQYY8jrPZXIhQFl0CDeVA](https://lh4.googleusercontent.com/XH6LZmDHeUyisk4C3gwsiLwXsc999TWhmsLfUqJL278as5Fg0oPs1JJuFdDUDe3fGUBN6fCSI5G0G8b3K5lLaiVCEH0rC5aHmw99WsfFWqOLFqWOxhKgOOmPVAh7e5qsgEQYY8jrPZXIhQFl0CDeVA)

Pada bagian **SSH Server**, kita mengisi informasi seperti

- Name	: nama remote server
- Hostname	: alamat IP/DNS remote server
- Username	: username yang digunakan untuk mengakses remote server
- Remote Directory	: directory yang digunakan jenkins untuk mengirim file

Setelah itu cek konfigurasi dengan mengklik **Test Configuration**. Jika berhasil, maka akan ada tulisan **Success**.

Untuk hostname, dapat dicek dengan mengakses machine vagrant lalu menggunakan perintah dibawah.

```bash
ip a
```

[https://lh3.googleusercontent.com/sqZqDT4mXaVuZ_5aOt4o0QLMqqRcZWUpRKvL-odROT-ndpAa266xN0nkH3zOMxSpdX8xmA3t9yU7Q5N1lhS4Y6ZtDJGtwQYvKWtsYFF0uXDHdiOkGKznwGNTmKyQCHTzMzYppxYYzXyvuSd4e2D7sA](https://lh3.googleusercontent.com/sqZqDT4mXaVuZ_5aOt4o0QLMqqRcZWUpRKvL-odROT-ndpAa266xN0nkH3zOMxSpdX8xmA3t9yU7Q5N1lhS4Y6ZtDJGtwQYvKWtsYFF0uXDHdiOkGKznwGNTmKyQCHTzMzYppxYYzXyvuSd4e2D7sA)

# **Create Project**

Kita akan membuat freestyle project pada Jenkins. Project ini nantinya kita konfigurasi sedemikian rupa agar dapat melakukan build, test dan deploy pada remote server.

[https://lh5.googleusercontent.com/Tf5NnQkC6FX35jUB4lmDMGEfO-isZsF3BbdGbxA0jyFR54n6qZQnSNOOuXWqfRRJLNi19VplC8Wj4LOquvw8evbC2FLa0VxSfPtHeec3v3TIFw41E4rgwWSviQzR3pvIyYk4mPZQWd2Xk1T0pUCsCg](https://lh5.googleusercontent.com/Tf5NnQkC6FX35jUB4lmDMGEfO-isZsF3BbdGbxA0jyFR54n6qZQnSNOOuXWqfRRJLNi19VplC8Wj4LOquvw8evbC2FLa0VxSfPtHeec3v3TIFw41E4rgwWSviQzR3pvIyYk4mPZQWd2Xk1T0pUCsCg)

Pada bagian **Source Code Management**, masukan repository project yang sudah di fork sebelumnya. Jangan lupa memasukan Credential Github. Jika lupa cara membuatnya silahkan baca kembali section sebelumnya.

[https://lh5.googleusercontent.com/t3c-xcEWA3lJL-IB3fkxdGkQK1tigr2Ev9S5oS3UqwcyZqqhqJlNmbpgZp1qNfUyFNTIdXrZvTXeXX5DCEFyjCH-VRlK8KTZnMMf4PgeV-tV7VUyauajhILWzkRExau9tr5WnG3OaEEJnOsPAw91jA](https://lh5.googleusercontent.com/t3c-xcEWA3lJL-IB3fkxdGkQK1tigr2Ev9S5oS3UqwcyZqqhqJlNmbpgZp1qNfUyFNTIdXrZvTXeXX5DCEFyjCH-VRlK8KTZnMMf4PgeV-tV7VUyauajhILWzkRExau9tr5WnG3OaEEJnOsPAw91jA)

Pada bagian Build Trigger, kita akan set menjadi **GitHub hook trigger for GITScm polling** sehinnga saat ada perubahan pada repository, maka webhook yang sudah kita setup sebelumnya akan mentrigger project untuk melakukan **build** dan **test**.

[https://lh3.googleusercontent.com/-FSglW1M6pmulyXagpJzGq9101gbYifX-6S42jstbeiVQQ8dBj5ZwCI4Vc0ZtOX9wVo1vWMlnFH_uHtB1A0nlXsXvQn_iuBsGsntzwdqsd1blkA19PggJQVySOEmby0724nOwzUWplo7J0CjR3539A](https://lh3.googleusercontent.com/-FSglW1M6pmulyXagpJzGq9101gbYifX-6S42jstbeiVQQ8dBj5ZwCI4Vc0ZtOX9wVo1vWMlnFH_uHtB1A0nlXsXvQn_iuBsGsntzwdqsd1blkA19PggJQVySOEmby0724nOwzUWplo7J0CjR3539A)

Selanjutnya pada bagian **Build**, kita akan melakukan build dan test pada aplikasi menggunakan NPM. Jika teman teman belum menginstal NodeJS, instal terlebih dahulu. Bisa dilihat [di sini](https://github.com/nodesource/distributions/blob/master/README.md#debinstall)**.**

Tambahkan build step **Execute Shell**.

[https://lh3.googleusercontent.com/fIVRlKKQDoeQChOjsO_gJh888d2-oc7lLNqe2wD4Vqo-fWrA6-k-czSBJ4CK1-zNqvJUNxw9L636H1EMRjgRd8LsVBXjDpkS7E4_NH3R8IgAX-ZgXSlwroPgSMtpHC8uYlNXgaEDGkwMoEkMR9aeIg](https://lh3.googleusercontent.com/fIVRlKKQDoeQChOjsO_gJh888d2-oc7lLNqe2wD4Vqo-fWrA6-k-czSBJ4CK1-zNqvJUNxw9L636H1EMRjgRd8LsVBXjDpkS7E4_NH3R8IgAX-ZgXSlwroPgSMtpHC8uYlNXgaEDGkwMoEkMR9aeIg)

Perintah yang diexecute disini adalah

```bash
npm install
npm run build
npm test
```

[https://lh4.googleusercontent.com/ewUIEk951SF4ASHpSoW1Z1t5Pl1ykEV6YSGiJJfFh9-MhSk0wx5Ak73A0ozl9MtsRYyNJikwK4qiYCdEySJjdOoXsSOXk-quDNmZycSR9w8zhQ-j7dzbRlYfn-XyRSPHAPzkXv4FhwBwFpjSQWNo9g](https://lh4.googleusercontent.com/ewUIEk951SF4ASHpSoW1Z1t5Pl1ykEV6YSGiJJfFh9-MhSk0wx5Ak73A0ozl9MtsRYyNJikwK4qiYCdEySJjdOoXsSOXk-quDNmZycSR9w8zhQ-j7dzbRlYfn-XyRSPHAPzkXv4FhwBwFpjSQWNo9g)

Setelah melakukan build dan test, hasil dari **build** tersebut akan kita simpan di remote server. Kita akan menambahkan build step Send files or execute commands over SSH.

[https://lh6.googleusercontent.com/nFoNfGGnPxJnlvYBz90JKUENIvM2fPtX1AyowrYP6gbzodYsjTlHHX5bFDh1-6XI_vGyK1Qx60qznhYIXZNXxQ-_JcCro2u5WS5GZQ5dhY67tD-idmcUOLjul-d6-xxDyVSP5GvtfgXfSovFKHIqeQ](https://lh6.googleusercontent.com/nFoNfGGnPxJnlvYBz90JKUENIvM2fPtX1AyowrYP6gbzodYsjTlHHX5bFDh1-6XI_vGyK1Qx60qznhYIXZNXxQ-_JcCro2u5WS5GZQ5dhY67tD-idmcUOLjul-d6-xxDyVSP5GvtfgXfSovFKHIqeQ)

Kita pilih remote server yang sudah kita konfigurasi sebelumnya, yaitu **Remote**.

Pada bagian Transfer ada beberapa bagian yang perlu kita perhatikan.

Source files		: file/folder yang akan dikirimkan ke remote server

Remove prefix		: prefix pengecualian file/folder yang akan dikirimkan

Remote directory	: directory  tujuan file/folder dikirim

Exec commands	: command yang dieksekusi pada remote server

[https://lh3.googleusercontent.com/VNKnp_1z7-tN2J2k0Uf1QFrfjXYFV3hGINYItEf0GzwSfvM9j3PlDHPVstVu4tAZ4XYemtM41fsEPvTBXHj4tlNzVbz9ARewlUobSqbnH36HS277irtj8Gksvo9-fq3wn1NrT06LjfgBJYBQtgn6NQ](https://lh3.googleusercontent.com/VNKnp_1z7-tN2J2k0Uf1QFrfjXYFV3hGINYItEf0GzwSfvM9j3PlDHPVstVu4tAZ4XYemtM41fsEPvTBXHj4tlNzVbz9ARewlUobSqbnH36HS277irtj8Gksvo9-fq3wn1NrT06LjfgBJYBQtgn6NQ)

Kita hanya butuh Source files dan Exec commands untuk diisi. Pada source file, kita masukan build/** yang maksudnya **folder build dan seluruh isinya**.

Command yang dieksekusi, kita mengganti nama directory build menjadi **react-todo** ini yang saya maksudkan directory khusus diawal.

Selanjutnya kita membuat symbolic link pada directory react-todo tersebut ke /var/www/html. Symbolic link ini berguna untuk mengclone isi dari directory **react-todo** ke ****/var/www/html. Jadi apapun perubahan yang ada di directory **react-todo**, maka di /var/www/html pun akan ikut berubah.

```bash
mv build react-todo
sudo ln -sf /home/vagrant/react-todo /var/www/html
```

Setelah itu, kita bisa menyimpan project kita.

# **Testing**

Untuk pengujian, kita tinggal **clone** repository yang sudah di **fork** diawal, lalu kita coba buat perubahan pada repository. Misalnya dalam case saya, saya mengubah file README.md.

[https://lh3.googleusercontent.com/M8qg910OctBNgcyIEHmp5SPtPbsOXBsxbo7nowxEeVLzTl3Qq1AHxiRa_jU__XfbpC3wC-Ic7pI-lsR9gj3kuasYwaoMqvEodDct1QmF_HcxtEV2DAHAgKDXmWoCGE3X03KcGC_oQPyRbWRDmQrPcw](https://lh3.googleusercontent.com/M8qg910OctBNgcyIEHmp5SPtPbsOXBsxbo7nowxEeVLzTl3Qq1AHxiRa_jU__XfbpC3wC-Ic7pI-lsR9gj3kuasYwaoMqvEodDct1QmF_HcxtEV2DAHAgKDXmWoCGE3X03KcGC_oQPyRbWRDmQrPcw)

Setelah itu, kita dapat melakukan commit dan push untuk mentrigger pipeline pada Jenkins.

```bash
git add .
git commit -m "modified readme"
git push
```

Jika kita lihat di project Jenkins, trigger berhasil dan pipeline yang kita buat berjalan.

[https://lh4.googleusercontent.com/101bImv2Ee_KHCDqWHn1Dw70GBhYC50t-Q11LiJXKAZ08fzDrFgx32v8xfOUjBwsJRhuPwZZD-nELB1TGSGLyMVv1NGB5uceb8QNxRBqFVn-e9s5pgpfkwoOy7fzFopV7zJCp01InMUcCfyaQ8iJ5w](https://lh4.googleusercontent.com/101bImv2Ee_KHCDqWHn1Dw70GBhYC50t-Q11LiJXKAZ08fzDrFgx32v8xfOUjBwsJRhuPwZZD-nELB1TGSGLyMVv1NGB5uceb8QNxRBqFVn-e9s5pgpfkwoOy7fzFopV7zJCp01InMUcCfyaQ8iJ5w)

Terakhir, pada browser, kita akses IP dari remote server tersebut dengan prefix **react-todo/**

[http://IP-remote-server/react-todo/](http://ip-remote-server/react-todo/)

[https://lh3.googleusercontent.com/Gj5g3nX4_DzKUBZoJvKKrWHVU6LIWeINU6_GKYOdBdDQpUyo4uSrE_E__J0N6ATQKz2SAoFvIVtCe0zwPmuqwn9FPEG-kHg1hoVK9P2kEWHpDmLoYXsihyyyP6ujx9c2HifVU6LjrL2p6ECDZipxVw](https://lh3.googleusercontent.com/Gj5g3nX4_DzKUBZoJvKKrWHVU6LIWeINU6_GKYOdBdDQpUyo4uSrE_E__J0N6ATQKz2SAoFvIVtCe0zwPmuqwn9FPEG-kHg1hoVK9P2kEWHpDmLoYXsihyyyP6ujx9c2HifVU6LjrL2p6ECDZipxVw)