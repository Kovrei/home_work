
# Домашнее задание к занятию «Индексы». Осипенков АЮ

### Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

### Решение 1

```mysql
SELECT 
TABLE_SCHEMA AS 'Название базы данных', 
SUM(INDEX_LENGTH) AS 'Общий размер индексов', 
SUM(DATA_LENGTH) AS 'Общий размер таблиц', 
ROUND((SUM(INDEX_LENGTH)/(SUM(DATA_LENGTH))*100),1) AS 'Процентное отношение'
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = 'sakila';
```

### Задание 2

Выполните explain analyze следующего запроса:
```mysql
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```
- перечислите узкие места;
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

### Решение 2

- перечислите узкие места;  
**Анализ команды explain analyze показал, что при запросе команды sum(payment.amount) OVER (PARTITION BY c.customer_id,f.title ) время (actual time) = 5058..8821. Это и есть узкое место для общего запроса**  
-> Window aggregate with buffering: sum(payment.amount) OVER (PARTITION BY c.customer_id,f.title )   (actual time=5058..8821 rows=642000 loops=1)  
-> Sort: c.customer_id, f.title  (actual time=5058..5153 rows=642000 loops=1)  
  
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.  

```mysql
explain analyze
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p
join rental r on p.payment_date = r.rental_date
join customer c on c.customer_id = r.customer_id 
join inventory i on i.inventory_id = r.inventory_id 
join film f on f.film_id = i.inventory_id 
where payment_date >= '2005-07-30' and payment_date < DATE_ADD('2005-07-30', INTERVAL 1 DAY)
```
![alt text](https://github.com/Kovrei/home_work/blob/main/sdbsql/05-db/img/12-05-1.PNG?raw=true)

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 3*

Самостоятельно изучите, какие типы индексов используются в PostgreSQL. Перечислите те индексы, которые используются в PostgreSQL, а в MySQL — нет.

*Приведите ответ в свободной форме.*
