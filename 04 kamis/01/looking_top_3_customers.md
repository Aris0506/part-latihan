## Siapa 3 customer yang paling banyak belanja di setiap bulannya?

2. Poin Kunci:
- Ranking Reset: Kita mau ranking-nya diulang dari nol setiap ganti bulan (Januari ada juaranya sendiri, Februari ada juaranya sendiri). jadi kita pakai PARTITION BY bulan.

3. Dua Versi Kebenaran:
- Versi Sales (sale_order): Siapa yang paling banyak pesan (Omset/Janji Bayar).

```sql 
/** "Kinerja Sales" (Marketing, Target, Omset Potensial) -> Pakai sale_order**/
--CTE 1 (SultanStats): Masak datanya (Totalin belanja).
WITH SultanStats AS (
    SELECT
        DATE_TRUNC('month', date_order) AS bulan,
        rp.name AS name_customer,
        SUM(so.amount_total) AS total_belanja
    FROM sale_order AS so
    JOIN res_partner AS rp ON so.partner_id = rp.id
    -- FILTER PENTING ODOO: HANYA YG SUDAH DEAL
    WHERE so.state IN ('sale', 'done') 
    GROUP BY 1, 2 -- (Shortcut: Group by kolom ke-1 dan ke-2)
),
-- CTE 2 (RankingProcess): Kasih nomor antrian (Ranking).
RankingProcess AS (
    SELECT
        bulan,
        name_customer,
        total_belanja,
        DENSE_RANK() OVER (PARTITION BY bulan ORDER BY total_belanja DESC) AS rangking
    FROM SultanStats
)
SELECT * FROM RankingProcess
WHERE rangking <= 3
ORDER BY bulan DESC, rangking ASC;
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