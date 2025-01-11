# Motion-Fall-Detection-Data_Slayer_Competition [Accuracy_96%]

# Latar Belakang
Jatuh adalah salah satu peristiwa yang sering kali berdampak serius bagi manusia, terutama pada kelompok rentan seperti lansia, pekerja di lingkungan berisiko tinggi, atau individu dengan gangguan mobilitas. Menurut data dari Organisasi Kesehatan Dunia (WHO), jatuh menjadi penyebab utama cedera serius, seperti patah tulang, trauma kepala, hingga kematian dini pada kelompok tertentu. Selain itu, insiden jatuh sering kali menimbulkan rasa takut dan kehilangan kepercayaan diri, yang pada akhirnya dapat menurunkan kualitas hidup. Oleh karena itu, deteksi dini dan akurasi dalam mengenali kejadian jatuh menjadi langkah krusial dalam upaya meningkatkan keselamatan dan memberikan intervensi yang cepat.

Fall detection atau deteksi jatuh adalah teknologi yang memungkinkan identifikasi peristiwa jatuh secara otomatis menggunakan sensor atau analisis data seperti video dan citra. Dengan perkembangan teknologi kecerdasan buatan, khususnya dalam bidang machine learning, deteksi jatuh kini dapat dilakukan dengan lebih cepat dan akurat melalui pemrosesan citra dan pengembangan model klasifikasi berbasis data. Meskipun demikian, teknologi ini masih menghadapi sejumlah tantangan, termasuk kekurangan data yang berkualitas, kesulitan membedakan kejadian jatuh dari aktivitas normal, dan keterbatasan dalam menggeneralisasi model untuk berbagai kondisi dunia nyata.

Dengan tema "Motion Matters: Human Fall Detection Classification for Safety Insight", Data Slayer 2.0 hadir sebagai ajang untuk mengembangkan solusi inovatif dalam deteksi jatuh manusia menggunakan machine learning. Kompetisi ini memberikan kesempatan kepada mahasiswa untuk mengeksplorasi data terkait, memahami pemrosesan citra, melakukan ekstraksi fitur yang relevan, dan membangun model klasifikasi yang akurat. Diharapkan kompetisi ini dapat mendorong penelitian lebih lanjut dalam teknologi deteksi jatuh dan menjadi pondasi awal bagi mahasiswa untuk mengembangkan inovasi di bidang ini.

# Metriks
Pada kompetisi ini, performa model akan dievaluasi menggunakan metrik Accuracy. Accuracy mengukur persentase prediksi yang benar terhadap seluruh data dalam dataset pengujian. Semakin tinggi nilai Accuracy, semakin baik performa model.

Rumus Accuracy:

Accuracy=Jumlah Prediksi Benar : Jumlah Total Observasi

# Alur Pengerjaan
![image](https://github.com/user-attachments/assets/08809840-dc5e-4eee-a800-9dc2922fdaf5)

Diagram di atas merupakan gambaran umum mengenai tahapan-tahapan penyelesaian masalah yang dilakukan dalam notebook ini. Dalam melakukan pengklasifikasian data, kami menggunakan dua pendekatan sebagai berikut :
- Temporal Frame - Pendekatan ini menggunakan penggabungan frame dalam urutan tertentu melalui *stacking* (Menumpuk frame dalam beberapa tumpukan yang telah ditentukan) yang menggunakan model 3D.
- Single Frame - Pendekatan ini menggunakan frame per satuan yang akan diprediksi menggunakan model 2D.

Penjelasan lebih detail setiap tahapan dan metode yang digunakan dapat ditemukan di bab selanjutnya

# Model
Model **`MobileNetV3Berdiri`** adalah model deep learning berbasis arsitektur **MobileNetV3-Large**, yang dioptimalkan untuk tugas klasifikasi gambar dengan efisiensi tinggi. Backbone **MobileNetV3-Large** digunakan sebagai feature extractor, di mana lapisan classifier bawaan digantikan oleh **`Identity`**, sehingga hanya fitur yang diekstraksi tanpa menghasilkan output prediksi langsung.

Setelah fitur diekstraksi oleh backbone, model menggunakan classifier custom yang terdiri dari beberapa lapisan fully connected dengan ukuran awal 512 neuron. Lapisan ini dilengkapi dengan **batch normalization**, fungsi aktivasi **ReLU**, dan **dropout** untuk mengurangi overfitting. Akhirnya, model menghasilkan output dua kelas melalui lapisan linear terakhir.

Model **`MobileNetV3TidakBerdiri`** adalah model klasifikasi gambar yang menggabungkan efisiensi backbone **MobileNetV3-Large** dengan mekanisme perhatian tambahan berbasis **CBAM (Convolutional Block Attention Module)**. MobileNetV3 digunakan sebagai feature extractor, di mana lapisan classifier bawaan dihapus dan digantikan oleh lapisan custom. Fitur yang dihasilkan oleh backbone diproses lebih lanjut oleh CBAM, yang secara adaptif memberikan perhatian pada saluran dan dimensi spasial untuk meningkatkan representasi fitur penting.

CBAM bekerja melalui dua tahap: **channel attention**, yang memprioritaskan saluran berdasarkan informasi global, dan **spatial attention**, yang memfokuskan perhatian pada lokasi spasial penting. Untuk menjaga konsistensi dimensi, model menggunakan koneksi residual yang menggabungkan fitur asli dan fitur yang diperkuat oleh CBAM. Setelah itu, fitur diringkas menggunakan **adaptive average pooling**, diratakan, dan diproses oleh lapisan fully connected yang dilengkapi dengan **batch normalization**, **ReLU**, dan **dropout** untuk klasifikasi dua kelas.

Model ini dioptimalkan menggunakan **AdamW**, dengan pengaturan learning rate yang disesuaikan melalui scheduler **StepLR**. Dengan penambahan CBAM dan fine-tuning pada MobileNetV3, model ini dirancang untuk menghasilkan performa yang lebih baik dalam klasifikasi dengan mempertahankan efisiensi komputasi.

# Kesimpulan
- <p style="font-size:18px;">Kualitas Dataset: Dataset yang kami gunakan sangat tidak bersih, sehingga memerlukan langkah pra-pemrosesan yang intensif untuk memastikan data siap digunakan dalam pemodelan.

- <p style="font-size:18px;">Untuk klasifikasi gerakan non_fall dan fall, didapatkan akurasi sebesar 0.93 dengan metode Temporal Frame dan didapatkan akurasi sebesar 0.95 dengan metode Single Frame yang keduanya merupakan skor yang didapatkan di sistem Kaggle. Metode yang kami gunakan dalam Temporal Frame berdasarkan paper dari [link text](https://pmc.ncbi.nlm.nih.gov/articles/PMC9470335/#sec4) menunjukkan performa yang cukup. Akan tetapi, jika dibandingkan menggunakan single frame didapatkan performa yang lebih bagus. Metode single frame berdasarkan analisis kami pada data train yang disediakan terhadap anomali data yang bisa saja diprediksi salah oleh metode sebelumnya.

- <p style="font-size:18px;"> Model : Dalam melakukan deteksi menggunakan metode Single Frame kita menggunakan arsitektur Mobilenet_v3_large dan untuk metode Temporal Frame menggunakan arsitektur M3D_18 dengan penambahan Attention Layer


- <p style="font-size:18px;" Proses Deteksi Gambar > Kami menggunakan arsitektur YOLO_V5 untuk proses deteksi gambar untuk membedakan berdiri dan tidak_berdiri.

# Big Thanks to 
[@eldntr](https://github.com/eldntr) [@fadhel](https://github.com/fadhel) sebagai satu tim saya yang menyumbangkan kemampuan coding dan pemikirannya pada proyek ini.

