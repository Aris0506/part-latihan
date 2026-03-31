 
# RFM analyst
 -- RFM analyst
SELECT
rp.name AS "Customer",
-- Recency (Kabar Terakhir)
CURRENT_DATE - MAX(so.date_order)::date AS "Hari Menghilang",
-- Frequency (Ke)
COUNT(so.id) AS "Jumlah Transaksi",
-- Monetary (Kekayaan)
ROUND(SUM(so.amount_total), 0) AS "Total Omzet"


FROM sale_order so
JOIN res_partner rp ON so.partner_id = rp.id
WHERE so.state IN ('sale', 'done')  -- Cuma hitung transaksi sah
GROUP BY rp.name
ORDER BY "Hari Menghilang" ASC;     -- Urutkan dari yang paling aktif


# BLUF (Bottom Line Up Front)
contoh
Pak, saya sarankan tim sales kita segera telepon PT Indofood hari ini juga. Alasannya, dari data yang saya tarik, mereka pernah belanja 336 juta tapi sudah 70 hari menghilang setelah satu kali transaksi, kita perlu tahu masalahnya apa supaya mereka mau belanja lagi.