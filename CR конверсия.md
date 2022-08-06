# SQL
my SQL code


### Этот код считает конверсию (CR) заказчиков в базе Northwind

#### Конверсия — одна из самых важных метрик. Она показывает, какая доля посетителей совершает целевое для бизнеса действие.

  [Cэмпл базы Northwind можно найти например тут ](https://github.com/pthom/northwind_psql/blob/master/northwind.sql)
 
```SQL 
 
 WITH a as (SELECT 
    DISTINCT(DATE_TRUNC('MONTH', order_date)::date) mnth,
    COUNT(DISTINCT customer_id) customers_this_month
    FROM northwind.orders
    WHERE DATE_TRUNC('MONTH', order_date)::date BETWEEN '1996-07-01' AND '1998-05-31'
    GROUP BY 1), --секция по поиску количества заказчиков в этом месяце
    
b as ( 
    SELECT 
    MIN(DATE_TRUNC('MONTH', order_date)::date) min_month,
    customer_id 
    FROM northwind.orders
    WHERE DATE_TRUNC('MONTH', order_date)::date BETWEEN '1996-07-01' AND '1998-05-31'
    GROUP BY 2
    ORDER BY 1), -- секция по поиску появления нового заказчика (по первому заказу)
    
c as (SELECT min_month, count(*) cnt
    FROM b 
    GROUP BY 1 
    ORDER BY 1) -- подсчет количества новых заказчиков по месяцу их первого заказа

SELECT 
    mnth, 
    customers_this_month, 
    SUM(cnt) OVER (order by mnth) as total_customers, -- сумма с накоплением по месяцам
    ROUND((customers_this_month::numeric/SUM(cnt) OVER (order by mnth)::numeric)*100, 2) "conversion" -- подсчет конверсии
FROM a
LEFT JOIN c ON a.mnth = c.min_month 
```
#### Датафрейм конверсии
 
 <img width="559" alt="CR dataframe" src="https://user-images.githubusercontent.com/107502986/182956474-cf380887-d6b5-4ebd-a399-882e6af256a0.png">
 
#### Визуализация
 
 <img width="739" alt="Конверсия" src="https://user-images.githubusercontent.com/107502986/183235730-8aba5811-ff6f-45a7-9b2b-ec9bd06b4d52.png">
