## Siapa 3 customer yang paling banyak belanja di setiap bulannya?

2. Poin Kunci:
- Ranking Reset: Kita mau ranking-nya diulang dari nol setiap ganti bulan (Januari ada juaranya sendiri, Februari ada juaranya sendiri). jadi kita pakai PARTITION BY bulan.

3. Dua Versi Kebenaran:
- Versi Sales (sale_order): Siapa yang paling banyak pesan (Omset/Janji Bayar).

```sql 
/** "Kinerja Sales" (Marketing, Target, Omset Potensial) -> Pakai sale_order**/
-- LANGKAH 1: Masak Data Mentah (CTE 1)
WITH SultanStats AS (
    SELECT
        -- Rumus Tanggal: Ubah tanggal harian jadi tanggal awal bulan (01 Jan, 01 Feb, dst)
        DATE_TRUNC('month', so.date_order) AS bulan,
        
        -- Ambil Nama Customer (bukan ID)
        rp.name AS name_customer,
        
        -- Totalin Omset
        SUM(so.amount_total) AS total_belanja

    FROM sale_order AS so
    JOIN res_partner AS rp ON so.partner_id = rp.id
    
    -- FILTER WAJIB ODOO:
    -- Hanya ambil yang sudah Confirm ('sale') atau Locked ('done')
    -- Abaikan Quotation ('draft') dan Cancel ('cancel')
    WHERE so.state IN ('sale', 'done')
    
    GROUP BY 1, 2 -- Grouping berdasarkan Bulan & Nama
), 

-- LANGKAH 2: Kasih Nomor Urut / Ranking (CTE 2)
RankingProcess AS (
    SELECT
        bulan,
        name_customer,
        total_belanja,
        
        -- WINDOW FUNCTION SAKTI:
        -- PARTITION BY bulan = Reset ranking jadi juara 1 lagi tiap ganti bulan
        -- ORDER BY total... DESC = Yang duitnya paling gede di atas
        DENSE_RANK() OVER (PARTITION BY bulan ORDER BY total_belanja DESC) AS rangking
    FROM SultanStats
)

-- LANGKAH 3: Filter & Sajikan (Main Query)
SELECT * FROM RankingProcess
WHERE rangking <= 3 -- Ambil cuma Top 3
ORDER BY bulan DESC, rangking ASC; -- Urutkan dari Bulan Terbaru & Juara 1
```

- Versi Finance (account_move): Siapa yang paling banyak setor duit (Real Paid).
``` sql
/**"Cashflow / Duit Masuk" (Keuangan, Bonus Tahunan) -> Pakai account_move**/
WITH SultanPaid AS (
    SELECT
        DATE_TRUNC('month', invoice_date) AS bulan, -- Pakai tgl invoice, bukan tgl SO
        rp.name AS name_customer,
        SUM(am.amount_total) AS total_uang_masuk -- Total dari Invoice
    FROM account_move AS am
    JOIN res_partner AS rp ON am.partner_id = rp.id
    WHERE 
        am.move_type = 'out_invoice'  -- Khusus Invoice Customer
        AND am.state = 'posted'       -- Sudah diposting
        AND am.payment_state = 'paid' -- HANYA YANG SUDAH LUNAS
    GROUP BY bulan, name_customer
), 
RankingProcess AS (
    SELECT
        bulan,
        name_customer,
        total_uang_masuk,
        DENSE_RANK() OVER (PARTITION BY bulan ORDER BY total_uang_masuk DESC) AS rangking
    FROM SultanPaid
)
SELECT * FROM RankingProcess
WHERE rangking <= 3
ORDER BY bulan DESC, rangking ASC;

```

# intinya
- Blok 1 (WITH): "Dapur Masak" (Tempat bersih-bersih data, JOIN, GROUP BY).
- Blok 2 (Window): "Stempel Nomor" (Tempat kasih ranking/nomor urut).
- Blok 3 (SELECT Akhir): "Penyajian" (Filter ambil yang ranking 1-3 aja).