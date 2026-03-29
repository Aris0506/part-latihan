# this focus are Coding, Pandas, dan Manipulasi Data for data analysis
1. Transforming DataFrames -> like fillna, change data type etc.
2. Aggregating DataFrames -> like groupby(), .resample() etc.
3. Slicing and Indexing DataFrames -> like Tolong tampilkan saham yang anjlok lebih dari 10% saja (df[df['Daily_Return'] < -0.10]), or menjadikan kolom tanggal sebagai Index (set_index).
4. Creating and Visualizing DataFrames
5. Combining/Merging DataFrames

# Cheat Sheet Pribadi (Sesuai Materi Kita):

1. Buka Data: df = pd.read_csv('namafile.csv')
2. Cek Data: df.info() atau df.head()
3. Ganti Tipe Angka: df['kolom'] = pd.to_numeric(df['kolom'])
4. Ganti Tipe Tanggal: df['kolom'] = pd.to_datetime(df['kolom'])
5. Isi Kosong (0): df.fillna(0)
6. Gabung Tabel (Kiri selamat): pd.merge(tabel1, tabel2, on='kunci', how='left')
7. Gabung Tabel (Semua selamat): pd.merge(..., how='outer')

`Saat latihan, BOLEH buka contekan ini. Lama-lama mata dan jari akan hafal sendiri.`