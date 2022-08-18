# 11. Membuat Jenkins Pipeline

Created: July 21, 2022 2:50 PM

Setelah memahami konsep dasar pipelining pada Jenkins, sekarang mari kita buat sebuah pipeline sederhana untuk melakukan **build** dan **test** pada aplikasi berbasi **React**.

Prerequisites:

- Apps ([https://github.com/sdcilsy/todo-react](https://github.com/sdcilsy/todo-react))
- Jenkins 2.x
- Node.js 16.x
- Github plugin installed
- Pipeline plugin installed

# **Fork Repository**

Langkah pertama adalah fork repository app yang akan digunakan ke development environment masing masing.

[https://lh4.googleusercontent.com/vqk8_opShZ69V54xvD7Uiw51knz2TOoKVX6_yxYeP5jfhK5xhfM3uvBXJldK9WTnAxmKz20oUG6phCqnq0mGer84wRsnyIGNo9p5wKSE73HQpxkfOe0VEoN2J0NNvR5TkCxZBeZ0yZnO3bXFoPUKww](https://lh4.googleusercontent.com/vqk8_opShZ69V54xvD7Uiw51knz2TOoKVX6_yxYeP5jfhK5xhfM3uvBXJldK9WTnAxmKz20oUG6phCqnq0mGer84wRsnyIGNo9p5wKSE73HQpxkfOe0VEoN2J0NNvR5TkCxZBeZ0yZnO3bXFoPUKww)

Setelah clone repository dan buka dengan text editor favorit masing masing.

# **Setup Webhook**

Kita akan menggunakan webhook agar setiap kali push terjadi pada repository, Jenkins secara otomatis akan menjalankan pipeline tersebut. Untuk cara setup webhook pada github ada pada section sebelumnya.

[https://lh5.googleusercontent.com/tN69dtP7mLoFv_6VmY4WiqgB6v824joTzCYPAwCHbh6-4Qv1wnRLwX7pZADMlCyOLBbJ4ZKlnL-5b1R0K6kSafXcb50tWnr8IqmapCBwThMsa6gvE3xTNon9514MwEeRlCTRD2fG-UlXSMP4J0PYRw](https://lh5.googleusercontent.com/tN69dtP7mLoFv_6VmY4WiqgB6v824joTzCYPAwCHbh6-4Qv1wnRLwX7pZADMlCyOLBbJ4ZKlnL-5b1R0K6kSafXcb50tWnr8IqmapCBwThMsa6gvE3xTNon9514MwEeRlCTRD2fG-UlXSMP4J0PYRw)

# **Create Pipeline**

Kita akan buat sebuah pipeline untuk stage build dan test aplikasi tersebut.

Pertama tama, buat sebuah file **Jenkinsfile** pada working directory repository yang sudah di clone sebelumnya.

Pipeline yang akan kita buat sederhana yaitu dengan stage build dan test.

```bash
pipeline {
    agent any

    stages {
        stage('build app') {
            steps {
                sh 'npm install'
            }
        }
        stage ('test app') {
            steps {
                sh 'npm test'
            }
        }
    }
}
```

Pada pipeline tersebut kita menggunakan syntax Declarative dengan node **Any** yang berarti semua stage akan dieksekusi pada **node** yang tersedia. Karena kita memiliki 1 node (localhost) yaitu jenkins itu sendiri, maka pipeline akan dijalankan pada mesin localhost. Kita dapat menggantinya dengan docker dengan catatan harus menginstall plugin **Docker Pipeline**.

Ada 2 stage yaitu “build app” dan “test app” dimana masing masing memiliki step dengan perintah shell. Hal ini bisa kita lakukan dengan menambahkan **sh** pada setiap perintah shell yang akan dipakai.

Lalu kita beralih untuk membuat item baru pada **Jenkins**. Buat item baru dengan tipe **Pipeline**. Nama bisa disesuaikan. Saya akan menggunakan nama **react-todo-pipeline**.

[https://lh6.googleusercontent.com/ylAmRNR2coGwTaAVN-kUzfz9WbuX-NwWRZxFrXSYHIMzx8dHMDhwHbtsHvlx5WqNqIUF397zoZIGQHfKqCs4iTS46cQcLzi517sNVZxHOQhgXIMMn9m85O0vLvA7DTVIEsTjhAUc_6nFT6Xf92xuRQ](https://lh6.googleusercontent.com/ylAmRNR2coGwTaAVN-kUzfz9WbuX-NwWRZxFrXSYHIMzx8dHMDhwHbtsHvlx5WqNqIUF397zoZIGQHfKqCs4iTS46cQcLzi517sNVZxHOQhgXIMMn9m85O0vLvA7DTVIEsTjhAUc_6nFT6Xf92xuRQ)

Untuk project bisa anda buat seperti deskripsi project tersebut. Ini optional.

Selanjutnya beralih kebagian Build Trigger, kita pilih **GitHub hook trigger for GITScm polling** agar jenkins mengecek apakah ada event push pada repository agar pipeline bisa berjalan.

[https://lh5.googleusercontent.com/Ns1p2Thr2mIdswH882-dXwL6oXD01bnepzyepMRi86XjqjmxVBYMdu-FYZ3mwI2x5yE1nBxDNAJAKzHIUnltE5mAO6IXE3ZWl2Ktq_Ms-ar5BK4-AfWQVms8lS7RTYRk6WiRKX1gfclKptmtg-nWoA](https://lh5.googleusercontent.com/Ns1p2Thr2mIdswH882-dXwL6oXD01bnepzyepMRi86XjqjmxVBYMdu-FYZ3mwI2x5yE1nBxDNAJAKzHIUnltE5mAO6IXE3ZWl2Ktq_Ms-ar5BK4-AfWQVms8lS7RTYRk6WiRKX1gfclKptmtg-nWoA)

Selanjutnya pada bagian **Pipeline**, pilih definisi **Pipeline script from SCM** karena kita memiliki Jenkinsfile pada repository.

[https://lh4.googleusercontent.com/bFdzjFAk-EMZuQcJyb3wseyib-ETlxG0oC4Dgpsn6v32Er3D4DnOuludnKwLqfDgXgzP6_Em0SVQoZfgIGAdgRq6g-5NpLAwSILSVf1qlkgpwwCZc6AvY0G_iBe13kyQTJSUV1XgobVqRPwj1vT34Q](https://lh4.googleusercontent.com/bFdzjFAk-EMZuQcJyb3wseyib-ETlxG0oC4Dgpsn6v32Er3D4DnOuludnKwLqfDgXgzP6_Em0SVQoZfgIGAdgRq6g-5NpLAwSILSVf1qlkgpwwCZc6AvY0G_iBe13kyQTJSUV1XgobVqRPwj1vT34Q)

Lalu pada bagian SCM, pilih Git. Isikan juga URL repository masing masing beserta credential akun github.

[https://lh4.googleusercontent.com/TSaqzI_AktWM7z5amGpBJXfVY5JTXWHXp7SHi0yNAmlbRUf5mWuQjIzGXTVFazEhYHoqKalUmHY4tCYdY_IK8LJT9asmLxG-L3Ii_RAuajlxnXtuqxNuLYEYcGMJf-kDofQMF7936k_T2YMA7cbGNQ](https://lh4.googleusercontent.com/TSaqzI_AktWM7z5amGpBJXfVY5JTXWHXp7SHi0yNAmlbRUf5mWuQjIzGXTVFazEhYHoqKalUmHY4tCYdY_IK8LJT9asmLxG-L3Ii_RAuajlxnXtuqxNuLYEYcGMJf-kDofQMF7936k_T2YMA7cbGNQ)

Sehingga keseluruhan konfigurasi pipeline kita menjadi berikut.

[https://lh4.googleusercontent.com/EQLcFBYmZMsHoSV9Im2DYZsE1qamCUP9wdO1Inedb_8Zhs_BwyZbBF4OwUYn9EUzjWDW6Dd34oZ3FpKIvCDRqPToXpj6gTur-DrEbIEq1GSo5NnoMWchiQ45tYgzC2kN5mX884RkZnMNYs5fTGvvqA](https://lh4.googleusercontent.com/EQLcFBYmZMsHoSV9Im2DYZsE1qamCUP9wdO1Inedb_8Zhs_BwyZbBF4OwUYn9EUzjWDW6Dd34oZ3FpKIvCDRqPToXpj6gTur-DrEbIEq1GSo5NnoMWchiQ45tYgzC2kN5mX884RkZnMNYs5fTGvvqA)

# **Push To Repository**

Setelah semua selesai, selanjutnya kita akan melakukan push ke repository untuk menyimpan perubahan yang terjadi, sekaligus untuk men-trigger pipeline.

```bash
git add .git commit -m "add jenkins pipeline"git push
```

# **Visualize Pipeline Stage**

Akses Jenkins untuk melihat progres dari pipeline kita. Setelah ada push ke repository kita, Jenkins secara otomatis menjalankan pipelinenya. Disini juga terlihat progres dan waktu eksekusi setiap stage  yang ada pada pipeline. Jika Jenkins tidak secara otomatis mengeksekusi pipeline, kita dapat mengeklik “Build Now”.

[https://lh4.googleusercontent.com/ly9RUhSSkAeXERZFXkvYKdn0Jb49MA6uWImEn9LRxDHR7vv00UQz6IYfmmYbO7G29Zzah_O3px05Nm7R3BuYLUOYjMHIzpE4Wu34CEHUi2d9c2RbHVr1tUV-vaikrHBQSS6IlAw2EQgxlIVSyUOJ3w](https://lh4.googleusercontent.com/ly9RUhSSkAeXERZFXkvYKdn0Jb49MA6uWImEn9LRxDHR7vv00UQz6IYfmmYbO7G29Zzah_O3px05Nm7R3BuYLUOYjMHIzpE4Wu34CEHUi2d9c2RbHVr1tUV-vaikrHBQSS6IlAw2EQgxlIVSyUOJ3w)

Berikut hasilnya jika semua stage sudah berhasil tereksekusi.

[https://lh5.googleusercontent.com/p_waYRlLemiMGcPI2tvc-fMGcLVD_4syrpxfGYdmqdhEpSWdt3cFojEEAC3zrRATYlXliMKbwQgm8WceJON6u0u8NY4fFnO0Zojo5nRyyuNWvkWNuT5UifVDjVsHyDhDLHYusghxAAv2-Li-qw0grQ](https://lh5.googleusercontent.com/p_waYRlLemiMGcPI2tvc-fMGcLVD_4syrpxfGYdmqdhEpSWdt3cFojEEAC3zrRATYlXliMKbwQgm8WceJON6u0u8NY4fFnO0Zojo5nRyyuNWvkWNuT5UifVDjVsHyDhDLHYusghxAAv2-Li-qw0grQ)