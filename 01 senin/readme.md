this focus are Coding, Pandas, dan Manipulasi Data for data analysis


# Cheat Sheet Pribadi (Sesuai Materi Kita):

1. Buka Data: df = pd.read_csv('namafile.csv')
2. Cek Data: df.info() atau df.head()
3. Ganti Tipe Angka: df['kolom'] = pd.to_numeric(df['kolom'])
4. Ganti Tipe Tanggal: df['kolom'] = pd.to_datetime(df['kolom'])
5. Isi Kosong (0): df.fillna(0)
6. Gabung Tabel (Kiri selamat): pd.merge(tabel1, tabel2, on='kunci', how='left')
7. Gabung Tabel (Semua selamat): pd.merge(..., how='outer')

`Saat latihan, BOLEH buka contekan ini. Lama-lama mata dan jari akan hafal sendiri.`