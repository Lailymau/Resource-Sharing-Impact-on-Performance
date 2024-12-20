# Exercise 7 - Impact of Resource Sharing on System Performance

## 📜 **Deskripsi Proyek**
Proyek ini memanfaatkan **RTOS (Real-Time Operating System)** pada mikrokontroler STM32 untuk mengontrol beberapa LED dengan prioritas dan tugas yang berbeda. Sistem ini menggunakan FreeRTOS untuk menunjukkan kemampuan multitasking dan pengelolaan sumber daya bersama.  
Proyek ini terdiri dari **empat LED**:  
1. **LED Hijau**: Berkedip cepat, prioritas normal.  
2. **LED Merah**: Berkedip lebih lambat, prioritas normal.  
3. **LED Oranye**: Berkedip sangat cepat, prioritas lebih tinggi.  
4. **LED Biru**: Menyala hanya jika terjadi konflik sumber daya (resource contention).  

---

## 🎯 **Tujuan Proyek**
- 🚀 Menunjukkan implementasi multitasking menggunakan FreeRTOS.  
- 🔒 Mengelola akses sumber daya bersama dengan aman menggunakan *critical section*.  
- 💡 Mengontrol kedipan LED dengan interval yang berbeda berdasarkan prioritas tugas.  
- ⚡ Menangani konflik sumber daya dan memberikan indikator visual melalui LED Biru.  

---

## 📂 Node Diagram
![image](https://github.com/user-attachments/assets/a2985024-1867-4acd-90a8-23330d7508dc)

---

## 🛠️ **Peralatan yang Dibutuhkan**
- 💻 **Perangkat Lunak**:
  - STM32CubeIDE
  - FreeRTOS
- 🔧 **Perangkat Keras**:
  - **Board STM32 Nucleo**
  - Empat LED (Hijau, Merah, Oranye, Biru)
  - Kabel Jumper

---

## 📋 **Langkah Implementasi**
### ⚙️ **1. Konfigurasi STM32CubeMX**
- Buka file `.ioc` menggunakan STM32CubeMX.
- Aktifkan kernel FreeRTOS melalui tab **Middleware**.
- Konfigurasi GPIO untuk mengontrol empat LED:
  - **LED Hijau (led1)**, **LED Merah (led2)**, **LED Oranye (led4)**, dan **LED Biru (led3)**.
- Atur *stack size* dan prioritas tugas pada kernel RTOS.

### 🧵 **2. Implementasi Tugas RTOS**
- Buat tugas-tugas untuk mengontrol LED:
  - `GreenTask` untuk LED Hijau (berkedip cepat).
  - `RedTask` untuk LED Merah (berkedip lebih lambat).
  - `OrangeTask` untuk LED Oranye (berkedip sangat cepat, prioritas tinggi).
  - Indikator konflik (LED Biru) diatur di fungsi `AccessSharedData()`.

### 🔧 **3. Mengelola Sumber Daya Bersama**
- Gunakan mekanisme *critical section* untuk menjaga integritas data.
- Implementasikan penanganan konflik sumber daya dengan menyalakan LED Biru jika lebih dari satu tugas mencoba mengakses sumber daya secara bersamaan.

### 🔄 **4. Jalankan dan Uji**
- Upload program ke board STM32 Nucleo.
- Perhatikan pola kedipan setiap LED dan indikator konflik pada LED Biru.

---

## 🌟 **Output**
- 🟢 **Green LED (led1)**:
  - Menyala (HIGH) saat GreenTask aktif.
  - Masuk ke critical section dengan mengakses fungsi AccessSharedData() yang menggunakan flag StartFlag.
  - Setelah akses selesai, LED dimatikan (LOW) setelah delay 200 ms.
  - Siklus diulang setiap 400 ms (200 ms ON dan 200 ms OFF).
- 🔴 **Red LED (led2)**:
  - Menyala (HIGH) saat RedTask aktif.
  - Masuk ke critical section dengan mengakses fungsi AccessSharedData() yang juga menggunakan flag StartFlag.
  - Setelah akses selesai, LED dimatikan (LOW) setelah delay 550 ms.
  - Siklus diulang setiap 1,1 detik (550 ms ON dan 550 ms OFF).
- 🟠 **Orange LED (led4)**:
  - LED akan berkedip (toggle) secara terus-menerus dengan interval 50 ms.
  - Tidak masuk ke critical section dan hanya menggunakan fungsi delay osDelay(50).
- 🔵 **Blue LED (led3)**:
  - Hanya menyala (HIGH) jika terjadi konflik saat mengakses resource dalam fungsi AccessSharedData().
  - Konflik terjadi apabila dua atau lebih task mencoba mengakses resource secara bersamaan tanpa pengaturan yang benar (contoh: StartFlag tidak bebas).
  - Setelah konflik selesai diatasi, LED biru dimatikan (LOW).
- Semua tugas berjalan mulus tanpa konflik berlebihan, menandakan implementasi multitasking berhasil.
- Tidak ada konflik sumber daya berlebihan karena penggunaan *critical section* yang tepat.
- Semua tugas berjalan stabil tanpa memengaruhi performa sistem.

---

## 📂 **Struktur Program**
### 🧵 **Tugas RTOS**
- `GreenTask` (Prioritas Normal):
  - Mengontrol LED Hijau untuk berkedip setiap 200 ms.
- `RedTask` (Prioritas Normal):
  - Mengontrol LED Merah untuk berkedip setiap 550 ms.
- `OrangeTask` (Prioritas Tinggi):
  - Mengontrol LED Oranye untuk berkedip setiap 50 ms.

### 🔧 **Fungsi Utama**
1. **AccessSharedData()**:
   - Mengelola akses sumber daya bersama menggunakan variabel *flag* (`StartFlag`).
   - Menyalakan LED Biru jika terjadi konflik.
2. **SimulateReadWriteOperation()**:
   - Mensimulasikan pengolahan data dengan *dummy loop* selama 500 ms.
3. **taskENTER_CRITICAL() dan taskEXIT_CRITICAL()**:
   - Digunakan dalam tugas hijau dan merah untuk menjaga integritas data saat mengakses sumber daya bersama.

---

## 🔍 **Penjelasan Tambahan**
- **LED Biru (Indikator Konflik)**:
  - Hanya menyala jika lebih dari satu tugas mencoba mengakses sumber daya bersama pada saat yang sama.
- **Prioritas Tugas**:
  - Tugas dengan prioritas lebih tinggi (LED Oranye) akan selalu mendapatkan akses CPU terlebih dahulu.
- **Critical Section**:
  - Digunakan untuk mencegah tugas lain mengakses sumber daya bersama hingga tugas saat ini selesai.

---

## 🧪 **Hasil Percobaan Normal**
https://github.com/user-attachments/assets/02c905cb-ee0c-4d9a-bdb1-fe7d34dd9d13

---

## 🧪 **Hasil Percobaan Konflik**
https://github.com/user-attachments/assets/8fbfeb49-37e5-4516-be43-54b0a597771f
