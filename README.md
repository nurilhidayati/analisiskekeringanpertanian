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
```
    var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
2. Perhitungan nilai Land Surface Temperature (LST) menggunakan metode Split Window Algorithm (SWA)
- Perhitungan FVC 
```
    var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
- Perhitungan LSE
```
    var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
- Perhitungan LST
```
    var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
4. Ekstraksi nilai NDVI dan LST dilakukan dengan mengambil sampel random berdasarkan area kajian pada lahan pertanian Kabupaten Mojokerto. Penentuan titik sampel menggunakan metode acak menggunakan fungsi ee.FeatureCollection.randomPoints(). Selanjutnya, nilai hasil ekstrasi NDVI dan LST diekport dalam format CSV.
```
    var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
6. Regresi linear antara nilai NDVI dan nilai LST menggunakan scatter plot bertujuan untuk memperoleh batas basah dan batas kering yang digunakan sebagai persamaan nilai indeks kekeringan TVDI. Regresi linear nilai NDVI dan LST dilakukan menggunakan Microsoft Excel
7. Perhitungan nilai Temperature-Vegetation Dryness Index (TVDI). Nilai TVDI menghasilkan nilai minimum: 0 dan maximum: 1
```
    var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
9. Visualisasi Peta TVDI mengacu pada Sandholt (2002) yang membagi nilai TVDI menjadi 5 kelas yaitu: kelas basah (hijau tua), kelas agak basah (hijau muda), kelas normal (kuning), kelas agak kering (oranye), dan kelas kering (merah)
```
    var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
11. Penyajian Peta TVDI menggunakan tampilan antarmuka Split Panel Map untuk memudahkan dalam membandingkan sebaran kekeringan pertanian setiap tahun perekamannya.
```
// Pendeskripsian citra yang dimasukkan pada layer split panel
var images = {
      
      'TVDI Oktober 2022': TVDI202210.sldStyle(sld_intervals),
      'TVDI Agustus 2022': TVDI202208.sldStyle(sld_intervals),
      'TVDI September 2021': TVDI202109.sldStyle(sld_intervals),
      'TVDI Agustus 2021': TVDI202108.sldStyle(sld_intervals),
      'TVDI Oktober 2020': TVDI202010.sldStyle(sld_intervals),
      'TVDI September 2020': TVDI202009.sldStyle(sld_intervals),
      'TVDI Oktober 2019': TVDI201910.sldStyle(sld_intervals),
      'TVDI Agustus 2019': TVDI201908.sldStyle(sld_intervals),
      'TVDI September 2018': TVDI201809.sldStyle(sld_intervals),
      'TVDI Agustus 2018': TVDI201808.sldStyle(sld_intervals),
      'TVDI September 2017': TVDI201709.sldStyle(sld_intervals),
      'TVDI Agustus 2017': TVDI201708.sldStyle(sld_intervals),
      'TVDI September 2016': TVDI201609.sldStyle(sld_intervals),
      'TVDI Agustus 2016': TVDI201608.sldStyle(sld_intervals),
      'TVDI Oktober 2015': TVDI201510.sldStyle(sld_intervals),
      'TVDI Agustus 2015': TVDI201508.sldStyle(sld_intervals),
      'TVDI Okober 2014': TVDI201410.sldStyle(sld_intervals),
      'TVDI September 2014': TVDI201409.sldStyle(sld_intervals),
      'TVDI Oktober 2013': TVDI201310.sldStyle(sld_intervals),
      'TVDI Juli 2013': TVDI201307.sldStyle(sld_intervals),
};
// Pembuatan peta split sisi kiri 
var leftMap = ui.Map(); 
leftMap.setControlVisibility(false); 
var leftSelector = addLayerSelector(leftMap, 2, 'top-left'); 
// Pembuatan peta split sisi kanan 
var rightMap = ui.Map(); 
rightMap.setControlVisibility(false); 
var rightSelector = addLayerSelector(rightMap, 1, 'top-right'); 
// Pengaturan layer peta untuk memilih citra yang ingin ditampilkan
function addLayerSelector(mapToChange, defaultValue, position)
{ 
    var label = ui.Label('Pilih Tahun');
    function updateMap(selection)
    {
      mapToChange.layers().set(0, ui.Map.Layer(images[selection])); 
    }
    
    // Konfigurasi layer terpilih, proses update pilihan
      var select = ui.Select({items: Object.keys(images), onChange: updateMap});
      select.setValue(Object.keys(images)[defaultValue], true); 
      var controlPanel = ui.Panel({widgets: [label, select], style: {position: position}}); 
      mapToChange.add(controlPanel); 
} 
/* 
 * Mengikat semuanya pada split panel 
 */ 
// Pembuatan peta perbandingan dalam splitpanel
var splitPanel = ui.SplitPanel({ 
  firstPanel: leftMap, 
  secondPanel: rightMap, 
  wipe: true, 
  style: {stretch: 'both'} 
});
// Menetapkan splitpanel yang berada di root UI
ui.root.widgets().reset([splitPanel]); 
var linker = ui.Map.Linker([leftMap, rightMap]); 
leftMap.centerObject(mojokerto,10.3); 
rightMap.centerObject(mojokerto,10.3);
```
13. Uji Usabilitas dilakukan dengan menyebarkan kuisioner melalui Google Form untuk mengetahui penilaian dan respon dari pengguna mengenai pengalamannya menggunakan Earth Engine Apps ini

## Credits
Earth Engine Apps ini dibuat sebagai oleh Nuril Hidayati syarat Proyek Akhir (PA), serta dibimbing oleh Bapak Dwi Setyo Aji, S.Si., M.Sc..


