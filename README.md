# WA Status Downloader

Ekstensi Chrome untuk mengunduh **gambar dan video status/story** langsung dari **WhatsApp Web**. Cukup buka status, lalu klik tombol **Unduh** yang muncul di samping layar video.

![Manifest V3](https://img.shields.io/badge/Manifest-V3-00a884)
![Platform](https://img.shields.io/badge/Chrome-Chromium-blue)
![Version](https://img.shields.io/badge/version-1.1.0-green)

<!-- Tambahkan screenshot di sini, contoh:
![Screenshot](docs/screenshot.png)
-->

## Fitur

- Tombol unduh otomatis muncul di samping kanan layar status, mengikuti posisi dan ukuran video.
- Mendukung **gambar** (JPG, PNG, WebP, GIF) dan **video** (MP4, WebM, 3GP).
- Nama file otomatis berformat waktu, contoh: `wa-status-20261022-163012.mp4`.
- Tampilan tombol modern dengan ikon SVG dan animasi saat diarahkan kursor atau saat mengunduh.
- Ringan dan tanpa izin tambahan: ekstensi hanya berjalan di `web.whatsapp.com`.
- Tidak mengirim data ke server mana pun. Semua proses berjalan di browser kamu.

## Instalasi

Ekstensi ini belum ada di Chrome Web Store, jadi dipasang secara manual lewat mode developer.

1. **Unduh** repositori ini:
   - Klik **Code > Download ZIP** lalu ekstrak, atau
   - Clone dengan Git:
     ```bash
     git clone https://github.com/maxnmrantau/wa-status-downloader.git
     ```
2. Buka Chrome dan masuk ke `chrome://extensions`.
3. Aktifkan **Developer mode** (pojok kanan atas).
4. Klik **Load unpacked**, lalu pilih folder `wa-status-downloader` (folder yang berisi `manifest.json`).
5. Buka atau refresh tab [WhatsApp Web](https://web.whatsapp.com).

Ekstensi ini juga berjalan di browser berbasis Chromium lain seperti Edge, Brave, dan Opera dengan langkah yang serupa.

## Cara Penggunaan

1. Buka [web.whatsapp.com](https://web.whatsapp.com) dan masuk ke akunmu.
2. Buka tab **Status**, lalu klik status milik kontak.
3. Tunggu gambar atau video selesai dimuat.
4. Klik tombol hijau **Unduh** di samping kanan layar status.
5. File tersimpan ke folder unduhan default browser. Notifikasi kecil akan muncul di bawah layar.

**Tips**
- Status berpindah otomatis. Tahan (pause) status dulu kalau ingin mengunduh yang tertentu.
- Kalau muncul pesan *"Gagal mengunduh"*, tunggu beberapa detik sampai media selesai dimuat, lalu klik lagi.

## Cara Kerja

Saat status dibuka, WhatsApp Web menampilkan media memakai alamat `blob:` di dalam halaman. Ekstensi ini:

1. Memantau halaman (`MutationObserver` dan interval) untuk mencari elemen `<video>` atau `<img>` bersumber `blob:` yang terlihat dan berukuran cukup besar.
2. Memilih media utama (bukan latar blur atau thumbnail kecil).
3. Menempatkan tombol di samping media tersebut.
4. Saat tombol diklik, mengambil data blob lalu menyimpannya sebagai file lewat atribut `download`.

Pendekatan ini tidak bergantung pada nama class CSS WhatsApp yang sering berubah, sehingga relatif tahan terhadap pembaruan tampilan.

## Struktur Proyek

```
wa-status-downloader/
├── manifest.json     # Konfigurasi ekstensi (Manifest V3)
├── content.js        # Logika deteksi media, penempatan tombol, dan unduhan
├── content.css       # Gaya tombol dan notifikasi
└── icons/
    ├── icon16.png
    ├── icon48.png
    └── icon128.png
```

## Kustomisasi

Beberapa nilai yang bisa diubah di `content.js`:

| Konstanta / baris        | Fungsi                                                    | Bawaan |
| ------------------------ | --------------------------------------------------------- | ------ |
| `MIN_SIZE`               | Ukuran minimum media (px) agar dianggap sebagai status    | `280`  |
| `GAP`                    | Jarak tombol dari tepi kanan video (px)                   | `14`   |
| `r.top + 10` di `place()` | Jarak tombol dari tepi atas video (px)                    | `10`   |

Format nama file diatur di fungsi `download()` (`wa-status-${ts()}.${ext}`).

Setelah mengubah file, buka `chrome://extensions`, klik ikon **reload** pada ekstensi, lalu refresh tab WhatsApp Web.

## Pemecahan Masalah

| Masalah                              | Solusi                                                                                   |
| ------------------------------------ | ---------------------------------------------------------------------------------------- |
| Tombol tidak muncul                  | Refresh tab WhatsApp Web (F5) dan pastikan status sudah selesai dimuat.                  |
| Tombol muncul di posisi yang aneh    | Ubah ukuran jendela sedikit atau refresh halaman. Sesuaikan `GAP` jika perlu.            |
| *"Gagal mengunduh"*                  | Tunggu media selesai dimuat, lalu klik lagi.                                             |
| File tidak tersimpan                 | Periksa pengaturan unduhan Chrome (`chrome://settings/downloads`) dan izin unduhan.      |
| Berhenti bekerja setelah update WA   | WhatsApp Web mungkin mengubah struktur halaman. Buka *issue* dengan detail dan versi browser. |

Untuk melihat pesan error, buka DevTools (F12) lalu tab **Console** dan cari log berawalan `[WA Status Downloader]`.

## Batasan

- Hanya berfungsi di **WhatsApp Web** (`https://web.whatsapp.com/*`), bukan aplikasi desktop atau seluler.
- Hanya mengunduh media yang memang sudah dapat kamu lihat. Ekstensi tidak melewati pengaturan privasi siapa pun.
- Karena WhatsApp Web dapat berubah sewaktu-waktu, perilaku ekstensi bisa terpengaruh setelah pembaruan.

## Privasi

- Ekstensi tidak mengumpulkan, menyimpan, atau mengirim data apa pun ke pihak luar.
- Tidak memakai izin khusus (`permissions`); hanya *content script* pada `web.whatsapp.com`.
- Seluruh kode terbuka dan bisa kamu periksa sendiri.

## Penafian

Proyek ini dibuat untuk keperluan pribadi dan edukasi. Ekstensi ini **tidak berafiliasi, disponsori, atau didukung oleh WhatsApp maupun Meta**. Gunakan hanya untuk menyimpan status yang dibagikan kepadamu, hormati privasi dan hak cipta pemilik konten, dan jangan menyebarkannya ulang tanpa izin. Penggunaan sepenuhnya menjadi tanggung jawab pengguna.

## Kontribusi

Saran dan perbaikan sangat diterima.

1. Fork repositori ini.
2. Buat branch baru: `git checkout -b fitur-baru`
3. Commit perubahanmu: `git commit -m "Tambah fitur baru"`
4. Push ke branch: `git push origin fitur-baru`
5. Buka *Pull Request*.

## Rencana Pengembangan

- [ ] Unduh semua status dari satu kontak sekaligus
- [ ] Nama file berdasarkan nama kontak
- [ ] Pilihan posisi tombol lewat halaman opsi
- [ ] Dukungan Firefox

## Lisensi

Disarankan memakai [MIT License](https://choosealicense.com/licenses/mit/). Tambahkan file `LICENSE` di repositori sebelum dipublikasikan.

---

Dibuat oleh [@maxnmrantau](https://github.com/maxnmrantau)
