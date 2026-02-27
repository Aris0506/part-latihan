
# filtering dulu hitung total belanja
``` sql 
WITH SultanPaid AS (
    SELECT
        DATE_TRUNC('month', invoice_date) AS bulan,
        rp.name AS nama_customer,
        SUM(am.amount_total) AS Total_uang_masuk_atau_belanja
    FROM account_move AS am
    JOIN rest_partner AS rp
    ON am.partner_id = rp.id
    WHERE
        am.move_type = 'out_invoice'
        AND am.state = 'posted'
        AND am.payment_state = 'paid'
    GROUP BY bulan, nama_customer
)
```