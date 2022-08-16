## Рассчитать LTV в первые шесть месяцев с момента регистрации пользователей (для зарегестировавшихся в 2019 году).

#### Определяем профиль пользователя

SELECT user_id, created_at
FROM tools_shop.users
WHERE EXTRACT(YEAR FROM created_at) = 2019
    AND user_id IN (SELECT DISTINCT user_id FROM tools_shop.orders)
    
    
#### Определить лайфтайм для каждой покупки и месяц регистрации пользователя. 
#### Преобразуйем предыдущий запрос таким образом, чтобы от даты регистрации остался месяц с типом date. 
#### Добавьте в запрос лайфтайм — количество месяцев с момента регистрации для каждого заказа.

SELECT u.user_id, DATE_TRUNC('month', u.created_at)::date,
    EXTRACT(MONTH FROM AGE(o.created_at::date, u.created_at::date))
FROM tools_shop.users as u
JOIN tools_shop.orders as o ON u.user_id=o.user_id
WHERE EXTRACT(YEAR FROM u.created_at) = 2019
    AND u.user_id IN (SELECT DISTINCT user_id FROM tools_shop.orders)
    
    
#### Добавляем в предыдущий запрос суммарную стоимость заказов(кумулятивную) для каждого юзера.

SELECT u.user_id, DATE_TRUNC('month', u.created_at)::date,
    EXTRACT(MONTH FROM AGE(o.created_at::date, u.created_at::date)),
    SUM(o.total_amt) OVER (partition BY u.user_id ORDER BY o.created_At)
FROM tools_shop.users as u
JOIN tools_shop.orders as o ON u.user_id=o.user_id
WHERE EXTRACT(YEAR FROM u.created_at) = 2019
    AND u.user_id IN (SELECT DISTINCT user_id FROM tools_shop.orders)
    
    
#### Месяц регистрации пользователя с типом date;
#### лайфтайм;
#### среднее значение ltv;
#### Срез по данным (первые шесть месяцев);

WITH
a as (SELECT DATE_TRUNC('month', u.created_at)::date as dt,
    EXTRACT(MONTH FROM AGE(o.created_at::date, u.created_at::date)) as lt,
    SUM(o.total_amt) OVER (partition BY u.user_id ORDER BY o.created_At) ltv
    FROM tools_shop.users as u
    JOIN tools_shop.orders as o ON u.user_id=o.user_id
    WHERE EXTRACT(YEAR FROM u.created_at) = 2019
    AND u.user_id IN (SELECT DISTINCT user_id FROM tools_shop.orders))
    
SELECT dt, lt,
    ROUND(AVG(ltv)::numeric, 2)
FROM a 
WHERE lt <=5
GROUP BY 1,2
ORDER BY 1,2

