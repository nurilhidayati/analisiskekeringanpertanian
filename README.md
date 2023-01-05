# PERITANI: Dashboard Pemantauan Sebaran Kekeringan Pertanian Kabupaten Mojokerto, Provinsi jawa Timur Tahun 2013-2022

## Tentang Earth Engine Apps
PERITANI merupakan dashboard yang manyajikan peta interaktif sebaran kekeringan pertanian di Kabupaten Mojokerto pada tahun 2013-2022. Informasi sebaran kekeringan pertanian diperoleh dari data citra Landsat 8 Collection 2 Tier 1 TOA Reflectance dengan interval perekaman dari perwakilan data tahun 2013 hingga 2022 saat musim kemarau. Metode yang digunakan untuk memetakkan sebaran kekeringan adalah Temperature Vegetation Dryness Index (TVDI) yang memanfaatkan dua data masukan yaitu Normalized Difference Vegetation Index (NDVI) dan Land Surface Temperature (LST). Keseluruhan pengolahan hingga penyajian data dilakukan menggunakan platform Google Earth Engine menggunakan bahasa pemrograman JavaScript

## Data yang Digunakan:
1. Citra Landsat 8 Collection 2 Tier 1 TOA Reflectance Path/Row 118/65 dengan interval perekaman dari perwakilan data tahun 2013 hingga 2022 saat musim kemarau pada rentang bulan Juli-Oktober. Data Citra Landsat 8 di dapatkan dari Earth Engine Data Catalog. (http://developers.google.com/earthengine/datasets/catalog/LANDSAT_LC08_C02_T1_TOA0
2. Data Shapefile Lahan Pertanian di Kabupaten Mojokerto dari Ditjen Prasarana dan Sarana Pertanian (http://sikomantap.psp.pertanian.go.id/monitoring/peta/list)
3. Data Shapefile Batas Administrasi Kecamatan di Kabupaten Mojokerto dari Badan Informasi Geospasial (http://tanahair.indonesia.go.id/portal-web)
4. Informasi Validasi Nilai NDVI dan LST dari Survei Lapangan pada Bulan Oktober 2022


## Metode yang Digunakan::
1. Perhitungan nilai Normalized Difference Vegetation Index (NDVI) diperoleh dari saluran merah (Band 4) dan saluran inframerah dekat (Band 5) pada Citra Landsat 8 Collection 2 Tier 1 TOA Reflectance.
```json
var ndvi201307 = img201307.normalizedDifference(['B5', 'B4']).rename('ndvi1');
```
2. Perhitungan nilai Land Surface Temperature (LST) menggunakan metode Split Window Algorithm (SWA)
3. Ekstraksi nilai NDVI dan LST dilakukan dengan mengambil sampel random berdasarkan area kajian pada lahan pertanian Kabupaten Mojokerto. Penentuan titik sampel menggunakan metode acak menggunakan fungsi ee.FeatureCollection.randomPoints(). Selanjutnya, nilai hasil ekstrasi NDVI dan LST diekport dalam format CSV.
4. Regresi linear antara nilai NDVI dan nilai LST menggunakan scatter plot bertujuan untuk memperoleh persamaan nilai indeks kekeringan TVDI
5. Perhitungan nilai Temperature-Vegetation Dryness Index (TVDI). Nilai TVDI menghasilkan nilai minimum: 0 dan maximum: 1
6. Visualisasi Peta TVDI mengacu pada Sandholt (2002) yang membagi nilai TVDI menjadi 5 kelas yaitu: kelas basah (hijau tua), kelas agak basah (hijau muda), kelas normal (kuning), kelas agak kering (oranye), dan kelas kering (merah)
7. Penyajian Peta TVDI menggunakan tampilan antarmuka Split Panel Map untuk memudahkan dalam membandingkan sebaran kekeringan pertanian setiap tahun perekamannya.
8. Uji Usabilitas dilakukan dengan menyebarkan kuisioner melalui Google Form untuk mengetahui penilaian dan respon dari pengguna mengenai pengalamannya menggunakan Earth Engine Apps ini

## Credits
Earth Engine Apps ini dibuat sebagai oleh Nuril Hidayati syarat Proyek Akhir (PA), serta dibimbing oleh Bapak Dwi Setyo Aji, S.Si., M.Sc..


