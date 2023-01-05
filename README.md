# PERITANI: Dashboard Pemantauan Sebaran Kekeringan Pertanian Kabupaten Mojokerto, Provinsi jawa Timur Tahun 2013-2022

## Tentang Project
Earth Engine Apps yang manyajikan peta interaktif sebaran kekeringan pertanian di Kabupaten Mojokerto pada tahun 2013-2022. Informasi sebaran kekeringan pertanian diperoleh dari data citra Landsat 8 Collection 2 Tier 1 TOA Reflectance dengan interval perekaman dari perwakilan data tahun 2013 hingga 2022 saat musim kemarau. Metode yang digunakan untuk memetakan sebaran kekeringan adalah Temperature Vegetation Dryness Index (TVDI) yang memanfaatkan dua data masukan yaitu Normalized Difference Vegetation Index (NDVI) dan Land Surface Temperature (LST). Keseluruhan pengolahan hingga penyajian data dilakukan menggunakan platform Google Earth Engine menggunakan bahasa pemrograman JavaScript
<p align="center">
  <img src="https://raw.githubusercontent.com/nurilhidayati/analisiskekeringanpertanian/main/dashboard.png", height="328">
</p>

## Data yang Digunakan:
1. Citra Landsat 8 Collection 2 Tier 1 TOA Reflectance Path/Row 118/65 dengan interval perekaman dari perwakilan data tahun 2013 hingga 2022 saat musim kemarau pada rentang bulan Juli-Oktober. Data Citra Landsat 8 di dapatkan dari Earth Engine Data Catalog. (http://developers.google.com/earthengine/datasets/catalog/LANDSAT_LC08_C02_T1_TOA0
2. Data Shapefile Lahan Pertanian di Kabupaten Mojokerto dari Ditjen Prasarana dan Sarana Pertanian (http://sikomantap.psp.pertanian.go.id/monitoring/peta/list)
3. Data Shapefile Batas Administrasi Kecamatan di Kabupaten Mojokerto dari Badan Informasi Geospasial (http://tanahair.indonesia.go.id/portal-web)
4. Informasi Validasi Nilai NDVI dan LST dari Survei Lapangan pada Bulan Oktober 2022


## Metode yang Digunakan:
1. Perhitungan nilai Normalized Difference Vegetation Index (NDVI) diperoleh dari saluran merah (Band 4) dan saluran inframerah dekat (Band 5) pada Citra Landsat 8 Collection 2 Tier 1 TOA Reflectance.
```
//Perhitungan Normalized Difference Vegetation Index (NDVI)  
var ndvi = img.normalizedDifference(['B5', 'B4']).rename('ndvi');
```
2. Perhitungan nilai Land Surface Temperature (LST) menggunakan metode Split Window Algorithm (SWA)
```
//Menentukan nilai Min dan Max NDVI
{var minNDVI = ee.Number(ndvi.reduceRegion({
reducer: ee.Reducer.min(),
geometry: mojokerto,
scale: 30,
maxPixels: 1e9
}).values().get(0));
var maxNDVI = ee.Number(ndvi.reduceRegion({
reducer: ee.Reducer.max(),
geometry: mojokerto,
scale: 30,
maxPixels: 1e9
}).values().get(0));}

//Mendeskripsikan nilai tanah
var soil = ee.Number(0.2)

//Menghitung nilai Fractional Vegetation Cover (FVC)
var fv =(ndvi.subtract(soil).divide(maxNDVI.subtract(soil))).rename('fv');

//Menghitung Land Surface Emissivity (LSE) pada Band 10 dan Band 11
//termal 10
var termal10= img.select('B10');

//termal 11
var termal11= img.select('B11');

//LSE10
var LSE10 = termal10.expression(
    '(0.971*(1-fvc))+(0.987*(fvc))',{
      'fvc': fv.select('fv'),}).rename('LSE10');

//LSE 11
var LSE11 = termal11.expression(
    '(0.977*(1-fvc))+(0.989*(fvc))',{
      'fvc': fv.select('fv'),}).rename('LSE11');

//Mendeskripsikan rerata LSE 
var m = LSE10.add(LSE11).divide(ee.Number(2)).rename('mLSE');

//Mendeskripsikan selisih LSE 
var deltam = LSE10.subtract(LSE11).rename('deltaLSE');

//Menghitung nilai Land Surface Temperature (LST)
var LST = img.expression(
    '(TB10+1.378*(TB10-TB11))+0.183*(deltam)**2+(-0.268)+(54.3+(-2.238)*0.0013)*(1-m)+(((-129.2)+(16.4*0.0013))*(deltam))-273.15', 
    {
     'TB10' : img.select('B10'),
     'TB11' : img.select('B11'),
     'm' : m1.select('mLSE'),
     'deltam' : deltam1.select('deltaLSE')
    }
  ).rename('LST');
```
3. Ekstraksi nilai NDVI dan LST dilakukan dengan mengambil sampel random berdasarkan area kajian pada lahan pertanian Kabupaten Mojokerto. Penentuan titik sampel menggunakan metode acak menggunakan fungsi ee.FeatureCollection.randomPoints(). Selanjutnya, nilai hasil ekstrasi NDVI dan LST diekport dalam format CSV.
```
//Mendeskripsikan sampel sampel
var sampel= ee.FeatureCollection.randomPoints(mojokerto);

//Menggabungkan citra NDVI DAN LST
var stacked = ndvi.addBands(LST2);

//Ekstraksi Nilai NDVI dan LST berdasarkan sampel
var imgSampel = stacked.sampleRegions({
  collection: sampel,
  scale:30
})

//Ekspor ke Tabel
Export.table.toDrive({
  collection: imgSampel1,
  description: 'NDVIdanLST',
  folder: 'PA_TVDI',
  fileNamePrefix:'NDVIdanLST',
  fileFormat: 'CSV'
});
```
4. Regresi linear antara nilai NDVI dan nilai LST menggunakan scatter plot bertujuan untuk memperoleh batas basah dan batas kering yang digunakan sebagai persamaan nilai indeks kekeringan TVDI. Regresi linear nilai NDVI dan LST dilakukan menggunakan Microsoft Excel
5. Perhitungan nilai Temperature-Vegetation Dryness Index (TVDI). Nilai TVDI menghasilkan nilai minimum: 0 dan maximum: 1
```
//Mendeskripsikan Batas Basah
var LSTmin1 = ndvi.expression(
    '((-3.4512 * ndvi) + 26.079)', {   //Didapatkan dari hasil regresi NDVI dan LST
    'ndvi': ndvi.select('ndvi1')});

//Mendeskripsikan Batas Kering
var LSTmax1 = ndvi.expression(
    '((-2.4359 * ndvi) + 34.807)', {   //Didapatkan dari hasil regresi NDVI dan LST
    'ndvi': ndvi.select('ndvi1')});
    
//Menghitung nilai Temperature Vegetation Dryness Index (TVDI)
var TVDI =(LST.subtract(LSTmin).divide(LSTmax.subtract(LSTmin)));
```
6. Visualisasi Peta TVDI mengacu pada Sandholt (2002) yang membagi nilai TVDI menjadi 5 kelas yaitu: kelas basah (hijau tua), kelas agak basah (hijau muda), kelas normal (kuning), kelas agak kering (oranye), dan kelas kering (merah)
```
//Simbolisasi nilai TVDI 
var sld_intervals =
  '<RasterSymbolizer>' +
    '<ColorMap type="intervals" extended="false" >' +
      '<ColorMapEntry color="#ffffff" quantity="0" label="Tidak Ada Data (NA)"/>' +
      '<ColorMapEntry color="#7a8737" quantity="0.2" label="Kelas Basah"/>' +
      '<ColorMapEntry color="#0ae042" quantity="0.4" label="Kelas Agak Basah" />' +
      '<ColorMapEntry color="#fff70b" quantity="0.6" label="Kelas Normal" />' +
      '<ColorMapEntry color="#ffaf38" quantity="0.8" label="Kelas Agak Kering" />' +
      '<ColorMapEntry color="#ff641b" quantity="1" label="Kelas Kering" />' +
    '</ColorMap>' +
  '</RasterSymbolizer>';
 
// Memisahkan hasil sesuai dengan kelas klasifikasi
var thresholds = ee.Image([0, 0.19, 0.39, 0.59, 0.79, 1]);

//Menampilkan hasil TVDI
Map.addLayer(TVDI.sldStyle(sld_intervals), {}, 'TVDI');
```
7. Penyajian Peta TVDI menggunakan tampilan antarmuka Split Panel Map untuk memudahkan dalam membandingkan sebaran kekeringan pertanian setiap tahun perekamannya.
```
// Pendeskripsian citra yang dimasukkan pada layer split panel
var images = {
      'TVDI1': TVDI1.sldStyle(sld_intervals),
      'TVDI2': TVDI2.sldStyle(sld_intervals),
};

// Pembuatan peta split sisi kiri 
var leftMap = ui.Map(); 
leftMap.setControlVisibility(false); 
var leftSelector = addLayerSelector(leftMap, 1, 'top-left'); 

// Pembuatan peta split sisi kanan 
var rightMap = ui.Map(); 
rightMap.setControlVisibility(false); 
var rightSelector = addLayerSelector(rightMap, 2, 'top-right'); 

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

//Mengikat semuanya pada split panel 
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
8. Uji Usabilitas dilakukan dengan menyebarkan kuisioner melalui Google Form untuk mengetahui penilaian dan respon dari pengguna mengenai pengalamannya menggunakan Earth Engine Apps ini

## Disclaimer
Hasil pengolahan data sudah dilakukan uji akurasi pada dua data masukan yang digunakan, karena akurasi yang baik dari data masukan diperlukan agar nilai TVDI yang dihasilkan juga dapat akurat. Hasil uji akurasi dilakukan pada dua data masukan yaitu NDVI dan LST menghasilkan nilai akurasi NDVI pada lahan pertanian sebesar 83,33% dan rata-rata ketelitian nilai LST pada lahan pertanian sebesar 91,49%.

## Credits
Earth Engine Apps ini dibuat sebagai oleh Nuril Hidayati syarat Proyek Akhir (PA), serta dibimbing oleh Bapak Dwi Setyo Aji, S.Si., M.Sc..


