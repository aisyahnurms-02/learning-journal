```python
import collections 
import collections.abc
from pptx import Presentation

prs = Presentation("Reverse_Engineering__Teori,_Aplikasi,_dan_Regulasiv2.pptx")
slides_data = []
for i, slide in enumerate(prs.slides):
    slide_text = []
    for shape in slide.shapes:
        if hasattr(shape, "text") and shape.text:
            slide_text.append(shape.text)
    slides_data.append(f"--- Slide {i+1} ---\n" + "\n".join(slide_text))

print("\n".join(slides_data[:15])) # print first 15 slides to inspect



```

```text
--- Slide 1 ---

--- Slide 2 ---

--- Slide 3 ---

--- Slide 4 ---

--- Slide 5 ---

--- Slide 6 ---

--- Slide 7 ---

--- Slide 8 ---

--- Slide 9 ---
Contoh
--- Slide 10 ---

--- Slide 11 ---

--- Slide 12 ---



```

```python
# Let's check why it's empty, maybe shapes are grouped or it's images, or let's dump all text regardless of hierarchy
for i, slide in enumerate(prs.slides):
    print(f"Slide {i+1}:")
    for shape in slide.shapes:
        if shape.has_text_frame:
            for paragraph in shape.text_frame.paragraphs:
                for run in paragraph.runs:
                    if run.text.strip():
                        print(f"  [Text] {run.text}")
        elif shape.shape_type == 6: # Group shape
            for subshape in shape.shapes:
                if subshape.has_text_frame:
                    print(f"  [Group Text] {subshape.text_frame.text}")



```

```text
Slide 1:
Slide 2:
Slide 3:
Slide 4:
Slide 5:
Slide 6:
Slide 7:
Slide 8:
Slide 9:
  [Text] Contoh
Slide 10:
Slide 11:
Slide 12:


```

```python
for i, slide in enumerate(prs.slides):
    print(f"Slide {i+1} shapes:")
    for shape in slide.shapes:
        print(f"  Shape Name: {shape.name}, Type: {shape.shape_type}")
    if slide.has_notes_slide and slide.notes_slide.notes_text_frame:
        print(f"  Notes: {slide.notes_slide.notes_text_frame.text}")


```

```text
Slide 1 shapes:
  Shape Name: Google Shape;12;g3d3cc1d96b3_0_0, Type: PICTURE (13)
  Notes: 
Slide 2 shapes:
  Shape Name: Google Shape;17;g3d3cc1d96b3_0_4, Type: PICTURE (13)
  Notes: 
Slide 3 shapes:
  Shape Name: Google Shape;22;g3d3cc1d96b3_0_8, Type: PICTURE (13)
  Notes: 
Slide 4 shapes:
  Shape Name: Google Shape;27;g3d3cc1d96b3_0_12, Type: PICTURE (13)
  Notes: 
Slide 5 shapes:
  Shape Name: Google Shape;32;g3d3cc1d96b3_0_16, Type: PICTURE (13)
  Notes: 
Slide 6 shapes:
  Shape Name: Google Shape;37;g3d3cc1d96b3_0_20, Type: PICTURE (13)
  Notes: 
Slide 7 shapes:
  Shape Name: Google Shape;42;g3d3cc1d96b3_0_24, Type: PICTURE (13)
  Notes: 
Slide 8 shapes:
  Shape Name: Google Shape;47;g3d3cc1d96b3_0_28, Type: PICTURE (13)
  Notes: 
Slide 9 shapes:
  Shape Name: Google Shape;52;g3d3cc1d96b3_0_32, Type: PICTURE (13)
  Shape Name: Google Shape;53;g3d3cc1d96b3_0_32, Type: TEXT_BOX (17)
  Notes: 
Slide 10 shapes:
  Shape Name: Google Shape;58;g3d3cc1d96b3_0_37, Type: PICTURE (13)
  Notes: 
Slide 11 shapes:
  Shape Name: Google Shape;63;g3d3cc1d96b3_0_41, Type: PICTURE (13)
  Notes: 
Slide 12 shapes:
  Shape Name: Google Shape;68;g3d3cc1d96b3_0_45, Type: PICTURE (13)
  Notes: 


```

## Week 01 — Pengenalan, Ruang Lingkup, Sejarah, serta Aspek Etika dan Legalitas Reverse Engineering

### Ringkasan

Pada minggu pertama, saya mempelajari fondasi awal dari mata kuliah Reverse Engineering (RE). Materi ini memberikan pengenalan komprehensif mengenai definisi, ruang lingkup, perkembangan sejarah, hingga batasan etika dan hukum yang mengatur aktivitas rekayasa balik ini, baik secara internasional maupun dalam konteks regulasi di Indonesia.

Melalui materi ini, saya menyadari bahwa reverse engineering bukan sekadar aktivitas teknis membongkar aplikasi, melainkan sebuah metodologi sistematis yang memerlukan pemahaman etika yang kuat. Pengetahuan regulasi seperti DMCA dan UU ITE menjadi batasan penting agar keahlian yang diperoleh selama satu semester ke depan tetap berada pada koridor hukum yang aman dan bertanggung jawab.

### Materi yang Dipelajari

**1. Apa itu Reverse Engineering dan Ruang Lingkupnya?**
Secara umum, rekayasa balik (*reverse engineering*) adalah proses menganalisis suatu sistem untuk mengidentifikasi komponen-komponennya dan hubungan antar komponen tersebut, guna membuat representasi sistem dalam bentuk lain atau pada tingkat abstraksi yang lebih tinggi. Berbeda dengan *Forward Engineering* yang merancang sistem dari spesifikasi menjadi produk jadi, RE bergerak dari produk jadi ke belakang untuk memahami desain dan logikanya.

Alur perbandingannya dapat digambarkan sebagai berikut:

```text
Forward Engineering:
Kebutuhan (Requirements) ──► Desain & Kode Sumber ──► Produk Jadi (Biner)

Reverse Engineering:
Produk Jadi (Biner) ──► Analisis & Disassembly ──► Abstraksi Logika / Kode Sumber

```

Dalam dunia Teknik Komputer, ruang lingkup RE mencakup analisis perangkat keras (*hardware*), protokol jaringan, hingga perangkat lunak (*software binary analysis*) untuk tujuan interoperabilitas, audit keamanan, maupun analisis malware.

**2. Sejarah Singkat Reverse Engineering**
Aktivitas *reverse engineering* awalnya berkembang pesat di industri manufaktur dan militer secara fisik (*hardware*), di mana suatu negara atau perusahaan membongkar perangkat mekanik milik kompetitor untuk memahami cara kerjanya. Seiring berkembangnya komputer, RE bergeser ke ranah digital dan *software*, terutama dipicu oleh kebutuhan interkoneksi sistem antar vendor yang berbeda pada era awal komputasi.

Kini, sejarah tersebut berevolusi menjadikan RE sebagai fondasi utama pertahanan siber modern, khususnya dalam membedah *malware* baru yang belum memiliki *signature*, serta menemukan celah keamanan (*vulnerability research*) sebelum dieksploitasi oleh pihak yang tidak bertanggung jawab.

**3. Aspek Hukum dan Legalitas (DMCA dan UU ITE)**
One of the most crucial points on this week was understanding the legality of RE. Di ranah internasional, aktivitas ini bersinggungan dengan *Digital Millennium Copyright Act* (DMCA) dan lisensi perangkat lunak (EULA), yang umumnya melarang rekayasa balik demi melindungi hak kekayaan intelektual.

Namun, hukum menyediakan pengecualian khusus (*safe harbors*) untuk tujuan tertentu:

* **Interoperabilitas:** Membuat dua perangkat lunak berbeda agar dapat saling berkomunikasi dan terintegrasi.
* **Keamanan Sistem:** Melakukan riset kerentanan demi meningkatkan proteksi sistem itu sendiri.

Di Indonesia, aktivitas ini diatur secara beririsan dalam **UU ITE (Undang-Undang Informasi dan Transaksi Elektronik)**. Melakukan akses ilegal atau memodifikasi sistem tanpa hak adalah pelanggaran pidana, sehingga *reverse engineering* wajib dilakukan pada sistem milik sendiri, sampel malware di lingkungan terisolasi, atau sistem yang telah mendapatkan izin tertulis (seperti dalam lingkup Bug Bounty).

**4. Etika dalam Reverse Engineering**
Selain aspek hukum tertulis, seorang analis keamanan harus memegang teguh etika profesi. Materi minggu ini mengenalkan konsep *Responsible Disclosure* (pengungkapan yang bertanggung jawab), yaitu etika ketika seorang Reverse Engineer menemukan celah keamanan pada suatu produk.

Langkah etisnya adalah melaporkan temuan tersebut secara privat kepada vendor pembuat aplikasi, memberikan waktu bagi mereka untuk memperbaikinya (*patching*), baru kemudian mempublikasikannya ke publik untuk edukasi. Praktik ini didukung oleh ekosistem modern melalui program *Bug Bounty*, di mana analis diberikan kompensasi finansial atas kerentanan yang mereka laporkan secara legal.

**5. Kaitan RE dengan Industri Modern & Keamanan**
Di akhir sesi perkenalan, dijelaskan bahwa RE merupakan *core skill* di bidang keamanan siber saat ini. Implementasinya meliputi:

* **Malware Analysis:** Membongkar file biner jahat untuk mengetahui dampaknya tanpa perlu menjalankannya di sistem produksi.
* **Vulnerability Research:** Mencari kelemahan pada aplikasi komersial atau open-source guna memperkuat pertahanan sebelum dieksploitasi pelaku kriminal siber.
* **Capture The Flag (CTF):** Sebagai sarana kompetisi dan asah kemampuan (*gamification*) memecahkan teka-teki logika biner.

### Hal Baru yang Saya Pelajari

Beberapa konsep baru yang saya peroleh pada minggu ini adalah:

* Perbedaan mendasar alur kerja antara Forward Engineering dan Reverse Engineering.
* Batasan hukum internasional (DMCA) dan domestik (UU ITE) terkait pembongkaran *software*.
* Pentingnya prinsip *Responsible Disclosure* saat menemukan celah keamanan pada produk masal.
* Pembagian ekosistem kompensasi legal melalui platform Bug Bounty.
* Konsep dasar interoperabilitas sebagai alasan hukum diizinkannya aktivitas RE.

### Insight Minggu Ini

Materi perkenalan minggu ini mengubah total perspektif saya mengenai *reverse engineering*. Awalnya saya mengira aktivitas ini ilegal dan identik dengan pembajakan perangkat lunak (*cracking*), namun ternyata RE adalah ilmu sains yang sah, legal, dan sangat krusial bagi pertahanan siber jika dilakukan dengan niat dan prosedur yang benar.

Saya juga menyadari pentingnya keseimbangan antara kemampuan teknis tinggi dan tanggung jawab moral. Menjadi seorang Reverse Engineer yang andal berarti harus siap tunduk pada kode etik keamanan sistem, karena alat dan teknik yang sama bisa digunakan untuk merusak (menjadi kriminal) atau melindungi (menjadi analis keamanan).

### Tools yang Berkaitan

* Platform Bug Bounty (HackerOne, Bugcrowd)
* Dokumen Regulasi (Salinan UU ITE Indonesia, DMCA Exemptions)
* Dokumen Lisensi Perangkat Lunak (EULA Analysis)
* Lingkungan Lab Terisolasi (Virtual Machines dasar)

### Refleksi Pembelajaran

#### Pemahaman Saya

Materi minggu pertama berhasil memberikan fondasi dan peta jalan (*roadmap*) yang jelas mengenai apa yang akan saya hadapi satu semester ke depan. Saya kini memahami posisi penting RE dalam siklus hidup *software* dan keamanan sistem, serta tahu batasan-batasan etis apa saja yang tidak boleh dilanggar sebagai mahasiswa Teknik Komputer.

#### Hal yang Masih Ingin Saya Pelajari

Saya masih penasaran dan ingin mempelajari lebih dalam kasus-kasus nyata di mana aktivitas *reverse engineering* berhasil menyelamatkan infrastruktur kritis dari serangan siber besar (seperti kasus Stuxnet atau WannaCry). Selain itu, saya juga ingin memahami bagaimana batasan eksplisit dari pasal-pasal UU ITE di Indonesia ketika kita melakukan riset keamanan pada aplikasi publik secara mandiri.

#### Kesimpulan Pribadi

Pertemuan awal ini memberikan kesadaran bahwa *reverse engineering* bukan sekadar keahlian mengulik baris kode, melainkan sebuah seni menganalisis teknologi dengan pemahaman legalitas yang ketat. Dengan pondasi etika dan hukum yang telah ditanamkan di minggu pertama ini, saya merasa memiliki kompas moral yang tepat untuk mulai mempelajari aspek teknis yang lebih dalam pada pertemuan-pertemuan berikutnya.
