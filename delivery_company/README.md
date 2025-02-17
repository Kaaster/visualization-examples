### Исходная структура данных
- orders - таблица с заказами
- order_lines - содержимое заказа
- warehouses - геоданные
- products - продукты

### Наиболее интересные DAX-код
1. Недельное стандартное отклонение спроса (кол-ва купленных ед. товара)
```
quantity week std = 
STDEVX.P(
    SUMMARIZE(order_lines,
        [order_weeknum],
        "quantity", [quantity order_lines]
    ),
    [quantity]
)
```
2. Сумма выручки относительно заказа в orders
```
total = 
CALCULATE(
    SUM(order_lines[total_line]),
    ALLEXCEPT(orders, orders[order_id])
)
```
3. LTV
```
LTV = 
DIVIDE(
    CALCULATE(
        [revenue retention],
        FILTER(
            ALL(retention_n_ltv[date_diff]),
            retention_n_ltv[date_diff] <= MAX(retention_n_ltv[date_diff])
        )
    ), 
    [users retention]
)
```
4. Ретеншн n-го дня
```
N-day retention rate = 
DIVIDE(
    [users retention],
    CALCULATE(
        [users retention],
        ALLEXCEPT(retention_n_ltv, retention_n_ltv[first_date]),
        retention_n_ltv[date_diff] = 0
    )
)
```
5. Присвоение значения в зависимости от принадлежнсти суммы выручки к перцентилю, относительно покупателя (колонка) 
```
M Score = 
SWITCH(TRUE(),
    rfm[revenue] <= PERCENTILE.INC(rfm[revenue], 0.20), 1,    
    rfm[revenue] <= PERCENTILE.INC(rfm[revenue], 0.40), 2, 
    rfm[revenue] <= PERCENTILE.INC(rfm[revenue], 0.60), 3, 
    rfm[revenue] <= PERCENTILE.INC(rfm[revenue], 0.80), 4,
    5
)
```
