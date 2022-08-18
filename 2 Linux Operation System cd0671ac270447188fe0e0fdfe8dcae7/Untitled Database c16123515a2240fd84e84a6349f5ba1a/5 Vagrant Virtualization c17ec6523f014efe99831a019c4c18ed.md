# 5. Vagrant Virtualization

Created: June 23, 2022 10:30 AM

Ketika kita melakukan development terhadap sebuah aplikasi terkadang kita membutuhkan tool - tool penting untuk membantu pada developer untuk membuat sebuah aplikasi. Ada beberapa masalah yang sering ditemui oleh seorang developer diantaranya adalah :

- Kesulitan ketika antar developer menggunakan sistem operasi yang berbeda.
- Kesulitan ketika server yang digunakan menggunakan sistem operasi yang berbeda.
- Kesulitan ketika project yang sedang dikerjakan tidak dapat berjalan di PC yang lain.

Dari beberapa kesulitan tersebut kita dapat meminimalkannya dengan menggunakan **Vagrant**. Apa itu vagrant ?

Vagrant adalah sebuah software yang menggunakan teknologi virtual machine dimana kita dapat membuat lingkungan development secara portable, konsisten dan lebih fleksibel.

[*Hashicorp Vagrant*](https://lh5.googleusercontent.com/AqwK66sUpvfNH2WS_ZJ9V2R8fpC1RoOcn1YhouKoCB8m8sxxi6PkmPURX6-PwFt2QixoBMazX7hhTbGW1W5RMJuvt_GjoUVNQnYO5__S4800i5GKK88EJFcznFvhv1LR2pBNgyi-HqYq72hsLw)

*Hashicorp Vagrant*

Dikarenakan vagrant menggunakan teknologi virtual machine maka kita membutuhkan software seperti virtualbox dan VmWare. Tujuannya adalah kita ingin membuat sebuah lingkungan development secara portable, contohnya misalnya pada saat production kita akan menggunakan sistem operasi ubuntu maka pada saat development kita akan menggunakan ubuntu sebagai sistem operasi sehingga pada saat proses deploy ke production diharapkan tidak ada lagi permasalahan yang muncul.

Ketika ingin melakukan duplikasi atau dibagikan kepada team developer yang lain maka kita tinggal menggunakan perintah vagrant sehingga secara otomatis vagrant akan melakukan konfigurasi pada PC masing - masing team developer sehingga mempermudah dalam development sebuah aplikasi.

1. **Instalasi Virtual Box**

Pada modul ini, kita akan menggunakan virtual box dikarenakan virtual box merupakan software open source dan lebih mudah dikonfigurasikan. Pertama-tama, buka terminal pada OS Ubuntu Anda dengan menekan tombol CTRL + ALT + T.

Setelah terminal terbuka masukkan public key dengen menjalankan perintah berikut.

```bash
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
```

[https://lh4.googleusercontent.com/2csuYa6M0mSukP9g97kU4kcug70HO7hLfV_z3gOw4o5Ck4JPOLW5l63ronziRAo84UgcPkXlPgSZj5hSW8sUYyXrp6bfuRp20KqVmD4-vduk13SrAoIpdcv-BQTZh98hvsF2KvPhGBdvbI3GfQ](https://lh4.googleusercontent.com/2csuYa6M0mSukP9g97kU4kcug70HO7hLfV_z3gOw4o5Ck4JPOLW5l63ronziRAo84UgcPkXlPgSZj5hSW8sUYyXrp6bfuRp20KqVmD4-vduk13SrAoIpdcv-BQTZh98hvsF2KvPhGBdvbI3GfQ)

Setelah itu tambahkan repository virtualbox menggunakan perintah berikut.

```bash
echo "deb [arch=amd64] http://download.virtualbox.org/virtualbox/debian $(lsb_release -sc) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
```

[https://lh4.googleusercontent.com/oMtSaZD7hDa7rLhtl3qRBnk4RQN77xt1EYpFpLyB1je1IQ7rY0kFTD_3MwOyHKDhiOOtfxcbELSF2-SbT7GZwfW39pn2cHjGw_SlzOnXJlblLi7yL60c2VRvpG6L8zhx_d7pGZctzr7a6TLI-A](https://lh4.googleusercontent.com/oMtSaZD7hDa7rLhtl3qRBnk4RQN77xt1EYpFpLyB1je1IQ7rY0kFTD_3MwOyHKDhiOOtfxcbELSF2-SbT7GZwfW39pn2cHjGw_SlzOnXJlblLi7yL60c2VRvpG6L8zhx_d7pGZctzr7a6TLI-A)

Kemudian gunakan perintah berikut untuk melakukan instalasi virtual box.

```bash
sudo apt update
sudo apt install virtualbox-6.1 virtualbox-ext-pack
```

Pilih OK pada user agreement license seperti pada gambar dibawah. Caranya adalah dengan menekan tombol TAB pada keyboard hingga tulisan OK terdapat highlight, kemudian tekan enter.

[https://lh6.googleusercontent.com/RNTQe01fwb1JaKCSDNivT-HVPGRZids_dcQJUHV8Jj-D8tdqw9ggcR8_9fTqD37H3HycAVPak6SazoCaBVZbBbi8hvL-0x4Cpc9vv28J4Mps_OqOj1VCn0mFPRP_fZ_WFMHk4D4-SHFb8rgw9Q](https://lh6.googleusercontent.com/RNTQe01fwb1JaKCSDNivT-HVPGRZids_dcQJUHV8Jj-D8tdqw9ggcR8_9fTqD37H3HycAVPak6SazoCaBVZbBbi8hvL-0x4Cpc9vv28J4Mps_OqOj1VCn0mFPRP_fZ_WFMHk4D4-SHFb8rgw9Q)

Pilih Yes pada prompt berikut, kemudian tunggu hingga instalasi selesai.

[https://lh5.googleusercontent.com/7vwOpRdfKem58Y4dD_B_RTpUVYumNYUe9nubhaYAa0QDWr-znY28hUOk2i1e9_BdHrj_uqNrNKZQLiTaigEWS3h81sM6gSO0Cj0byPgMhoY_I0q1s068kBVGbHv-vqLATqcbJbbTAVj6rOSxvg](https://lh5.googleusercontent.com/7vwOpRdfKem58Y4dD_B_RTpUVYumNYUe9nubhaYAa0QDWr-znY28hUOk2i1e9_BdHrj_uqNrNKZQLiTaigEWS3h81sM6gSO0Cj0byPgMhoY_I0q1s068kBVGbHv-vqLATqcbJbbTAVj6rOSxvg)

# **Instalasi Vagrant**

Untuk melakukan instalasi vagrant silahkan download vagrant terlebih dahulu di [vagrant download](https://www.vagrantup.com/downloads.html), disini kita menggunakan ubuntu 18.04 64 bit maka kita pilih versi debian 64 bit. Untuk melakukan instalasinya silahkan jalankan perintah.

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install vagrant
```

## **Instalasi Box Vagrant**

Box pada vagrant berfungsi sebagai virtual machine yang memuat sistem operasi dan disana juga terdapat seluruh konfigurasi yang kita lakukan. Dengan demikian maka secara tidak langsung sebenarnya kita menggunakan virtual machine maka oleh karena itu kita membutuhkan virtual box sebagai tempat virtual machine.

Mengapa kita menggunakan vagrant ? mengapa tidak menggunakan virtual box saja ?

Salah satu alasannya adalah ketika menggunakan vagrant maka vagrant akan melakukan semua konfigurasi hanya dengan satu perintah pada saat sistem operasi start up sedangkan jika kita menggunakan virtual box maka kita harus melakukan konfigurasi secara manual.

Repository box bisa dilihat di [repository box](https://app.vagrantup.com/boxes/search). Berikut adalah bentuk umum untuk melakukan instalasi box.

```bash
vagrant box add user/box
```

bisa dilihat bahwa untuk melakukan instalasi box sangatlah mudah yaitu kita hanya menggunakan perintah diatas dan anda hanya perlu mengganti perintah user/box. Perintah user berarti adalah pembuat box sedangkan perintah box  adalah box yang ingin kita gunakan. Disini kita akan menggunakan box ubuntu/trusty64 maka jalankan perintah berikut.

```bash
vagrant box add ubuntu/trusty64
```

[https://lh5.googleusercontent.com/sxhAGyMbdqv9RscXZanMzW-izOzSC4iU8Yj_9MI_t8pmWBvjzlsrVIUxLTnIv7ocd5j40lQfWhh1ptG5EhDrUS6NSJLIZTPsGoBZEGNcU7iPmrXF8g477xi6uCAtiV0DXGy2DWCrfOTT0Cv4yA](https://lh5.googleusercontent.com/sxhAGyMbdqv9RscXZanMzW-izOzSC4iU8Yj_9MI_t8pmWBvjzlsrVIUxLTnIv7ocd5j40lQfWhh1ptG5EhDrUS6NSJLIZTPsGoBZEGNcU7iPmrXF8g477xi6uCAtiV0DXGy2DWCrfOTT0Cv4yA)

**PERHATIAN !**

Untuk melakukan instalasi box membutuhkan koneksi internet dan membutuhkan instalasi yang lama dikarenakan kita akan mendownload sebuah sistem operasi yang berkisar 1 GB > tergantung dari sistem operasi yang akan digunakan.

## **Membuat Project**

Untuk membuat project, silahkan buat sebuah folder misalnya disini kita akan membuat folder vagrantproject. Lakukan perintah berikut untuk membuat direktori vagrantproject.

[https://lh4.googleusercontent.com/pZM68yXAiRtd4Lr8o-ySK3aQZxTneiaJZxKWeiHugjXq0U0b5Xd4wfK2HnoxjOKvaIRGjkX8VwX5DPrUeJUNkw6ICBBjH-l1UW4lJ5XE1IJPV7CR8WKzBsYe7ZlBh0eGk2uT6hlSoJlH64rgow](https://lh4.googleusercontent.com/pZM68yXAiRtd4Lr8o-ySK3aQZxTneiaJZxKWeiHugjXq0U0b5Xd4wfK2HnoxjOKvaIRGjkX8VwX5DPrUeJUNkw6ICBBjH-l1UW4lJ5XE1IJPV7CR8WKzBsYe7ZlBh0eGk2uT6hlSoJlH64rgow)

Kemudian jalankan perintah berikut di dalam folder tersebut. Perintah ini akan menambahkan box Ubuntu 20.04 (Focal Fossa).

```bash
vagrant init ubuntu/focal64
```

maka akan muncul output seperti berikut.

[https://lh6.googleusercontent.com/mXJ7zbrSqQCLOezrYnqI8pb7gWjDb4RMNd1UiLVSGPmhI4bxLRDjhKUHCU8eIJ2Afit5ytbABPb0U9U3mFdEnpPjQSR5Cjeh1VjuPz_A2HVpfqRBXCM8tGad0o9I4-_pZGLsEHXMe4MR8DrPsA](https://lh6.googleusercontent.com/mXJ7zbrSqQCLOezrYnqI8pb7gWjDb4RMNd1UiLVSGPmhI4bxLRDjhKUHCU8eIJ2Afit5ytbABPb0U9U3mFdEnpPjQSR5Cjeh1VjuPz_A2HVpfqRBXCM8tGad0o9I4-_pZGLsEHXMe4MR8DrPsA)

Setelah proses diatas, maka akan muncul sebuah file bernama Vagrantfile. Kita bisa lihat dengan perintah

```bash
ls
```

[https://lh4.googleusercontent.com/HiuOFMqPh9uHoFtMyiBc4JC_BCXdipnpKDcZwcHcelGX5U-QvhOLEJLz0FqJeg_K-t_0_YaoWexTcoJKskXUAw8YJZHBdRI3Jphw0bv2X1-VVzfUzmPd-uLB4lwWuYLQHFXtPOAHJjg4wJx9mg](https://lh4.googleusercontent.com/HiuOFMqPh9uHoFtMyiBc4JC_BCXdipnpKDcZwcHcelGX5U-QvhOLEJLz0FqJeg_K-t_0_YaoWexTcoJKskXUAw8YJZHBdRI3Jphw0bv2X1-VVzfUzmPd-uLB4lwWuYLQHFXtPOAHJjg4wJx9mg)

Selanjutnya, untuk menjalankan silahkan jalankan perintah berikut.

```bash
vagrant up
```

Maka akan tampil output berikut pada terminal.

[https://lh6.googleusercontent.com/E9UMbsZcz67_ec4UTRpuqBJq0t3hKpo9LVdLj3KnPHdFgtQqCuleTvwokPmsTGvL8YVbCiMvy-6K2eN2DX3_zLyCMqhy1NGnbBjkZVumNav8f_tbNViqPtH2zfuWcVO0IaHaz8c-Ea5HZzPrsg](https://lh6.googleusercontent.com/E9UMbsZcz67_ec4UTRpuqBJq0t3hKpo9LVdLj3KnPHdFgtQqCuleTvwokPmsTGvL8YVbCiMvy-6K2eN2DX3_zLyCMqhy1NGnbBjkZVumNav8f_tbNViqPtH2zfuWcVO0IaHaz8c-Ea5HZzPrsg)

Jika box telah jalan, langkah selanjutnya adalah login ke dalam box yang telah kita jalankan. Untuk login ke dalam box silahkan jalankan perintah berikut.

```bash
vagrant ssh
```

Jika berhasil maka akan muncul output pada terminal seperti berikut.

[https://lh4.googleusercontent.com/TasywBLk28YYf1kdxqRsc82BTkFBt_3QD7vwU4iZFZabd35CmZqIJbTvPJS6QEOasnTZepn0EQ1ywD5SWOMZ2kDx3rotwGfvxRUXdAjJ8RcW1NZtzpJ8W3F_qOPszT4VVBmb5BfVIyzO7NzO3A](https://lh4.googleusercontent.com/TasywBLk28YYf1kdxqRsc82BTkFBt_3QD7vwU4iZFZabd35CmZqIJbTvPJS6QEOasnTZepn0EQ1ywD5SWOMZ2kDx3rotwGfvxRUXdAjJ8RcW1NZtzpJ8W3F_qOPszT4VVBmb5BfVIyzO7NzO3A)

Instalasi box pada vagrant telah berhasil. Sekarang kita bisa memulai belajar Linux tanpa takut error berpengaruh pada sistem operasi kita.

Namun bagaimana kita berkomunikasi dengan box Vagrant ini ? Yang dimaksudkan disini itu adalah bagimana kita dapat melakukan **ping** ke box Vagrant. Oleh karena itu kita harus mengatur networking dari box kita ini.

Ada 2 cara yang disediakan Vagrant untuk mengatur IP box. **Public** atau **Private**.

Public berarti box dapat diakses dari luar jaringan. Private berarti hanya box dan laptop fisik yang dapat berkomunikasi.

Kita hanya cukup mengubah konfigurasi file **Vagrantfile** dengan cara hapus karakter **#** pada bagian seperti gambar.

[https://lh4.googleusercontent.com/tup01aPMGWbHfzKTJyStICghlowNkufkpgvlvogYtchuKJkm3zpROwqV-YVCeeEFZ6TMHnN0wHvEoEvW-2n6GU5G5zAj08cu2g_b1JvbAtFw0yZhathspOS2uW5Qbu3Er2f3gzwFsY7NPsfsMg](https://lh4.googleusercontent.com/tup01aPMGWbHfzKTJyStICghlowNkufkpgvlvogYtchuKJkm3zpROwqV-YVCeeEFZ6TMHnN0wHvEoEvW-2n6GU5G5zAj08cu2g_b1JvbAtFw0yZhathspOS2uW5Qbu3Er2f3gzwFsY7NPsfsMg)

Sehingga seperti ini.

[https://lh5.googleusercontent.com/6JIqnmApn3FPWqJS6if2bZGGrd6v0lIISS6hHnHbZfP-yVQiYyQ9x_IMvxNdFTYYYuxFUAZSQ_CyLgqJAFPiK0jd5HnPIbpFaIoB9kNHzc4PF98rZZTtlYsBUORIV22o9oj2Gs1X2L6ToX8Drg](https://lh5.googleusercontent.com/6JIqnmApn3FPWqJS6if2bZGGrd6v0lIISS6hHnHbZfP-yVQiYyQ9x_IMvxNdFTYYYuxFUAZSQ_CyLgqJAFPiK0jd5HnPIbpFaIoB9kNHzc4PF98rZZTtlYsBUORIV22o9oj2Gs1X2L6ToX8Drg)

Setelah itu save file dengan menekan kombinasi Ctrl+x, lalu Ctrl+Y, dan Enter.

Setelah itu, kita lakukan **reload** pada box untuk menerapkan perubahan. Lalu kita akan ditanya interface mana yang akan dijadikan **Bridge**. Disini saya menggunakan interface Wi-Fi yang ditandai dengan huruf w didepan nama interface nya yang bernomorkan 1.

[https://lh6.googleusercontent.com/fS_BBVqQ2k0dDt8nNyx9LWS29Gj2PjMQXiTfUxZVI6UQCpSiTZ1Y1Zwan-h8SsBazPLDPyxYjXiVfFqkSKpDub5MWm3P74mviOqDGmPAAZfn7d4NoAjG2fJqSC6rQQ14nnOolCQ57AO2PAVOVg](https://lh6.googleusercontent.com/fS_BBVqQ2k0dDt8nNyx9LWS29Gj2PjMQXiTfUxZVI6UQCpSiTZ1Y1Zwan-h8SsBazPLDPyxYjXiVfFqkSKpDub5MWm3P74mviOqDGmPAAZfn7d4NoAjG2fJqSC6rQQ14nnOolCQ57AO2PAVOVg)

Setelah itu kita masuk ke box lagi menggunakan perintah **vagrant ssh**. Sekarang kita melihat ada interface baru yaitu **enp0s8** dengan IP seperti gambar.

[https://lh5.googleusercontent.com/nSBY-O4zCLYlIR33K6Rax5HJdbfjKjk_Yo0Npb3i17igjDBAazmBj-zYbmwcxCNn1uBO5-hZ6rFOg1O73yD-d_Ev68T_kIGCcvdSH10pmIo54Sh3fl77jINiNwb3Wkh3GlI2lYnqoJqjg1skOA](https://lh5.googleusercontent.com/nSBY-O4zCLYlIR33K6Rax5HJdbfjKjk_Yo0Npb3i17igjDBAazmBj-zYbmwcxCNn1uBO5-hZ6rFOg1O73yD-d_Ev68T_kIGCcvdSH10pmIo54Sh3fl77jINiNwb3Wkh3GlI2lYnqoJqjg1skOA)

Kita lakukan ping dari fisik laptop ke box.

[https://lh4.googleusercontent.com/ypWWak4j9eoRtbqBwXJwMxnqEqewR5yhWl714U0SO3kCUpWTqMm8te9CBiyYrMVfAb9Gd0WvAjEKujT4IMvmXmc-9hAIdeDjyW3iT6TqcpCzOTgX5_nciMDPfS91ViVFlyaIxtwaYqtCu7SNEA](https://lh4.googleusercontent.com/ypWWak4j9eoRtbqBwXJwMxnqEqewR5yhWl714U0SO3kCUpWTqMm8te9CBiyYrMVfAb9Gd0WvAjEKujT4IMvmXmc-9hAIdeDjyW3iT6TqcpCzOTgX5_nciMDPfS91ViVFlyaIxtwaYqtCu7SNEA)