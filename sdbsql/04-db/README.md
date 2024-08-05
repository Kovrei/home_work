
# Домашнее задание к занятию «SQL. Часть 2». Осипенков АЮ

### Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

### ответ

```mysql
select concat(s.last_name, ' ',s.first_name) as name, c2.city, count(c.store_id) as store
from customer c
join staff s on c.store_id = s.store_id 
join address a on c.store_id = a.address_id 
join city c2 on c2.city_id = a.city_id 
group by s.staff_id 
HAVING COUNT(c.store_id) > 300
```

### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

### ответ 

```mysql
select title, `length` 
from film 
where `length` > (select avg(`length`) from film)
```
```mysql
select title, `length` , avg_lenght.avg_lenght
from film 
cross join (select avg(`length`) avg_lenght from film f) avg_lenght
where `length` > avg_lenght.avg_lenght
```
### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

### ответ

```mysql
select date_format(payment_date, '%W %M %Y') as date, count(payment_id) as payment, sum(amount) as sum
from payment p 
group by date_format(payment_date, '%W %M %Y')
order by sum(amount) desc limit 1
```

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 4*

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

### ответ

```mysql
select concat(s.last_name, ' ',s.first_name) as name, count(p.payment_id),
case 
when count(p.payment_id) > 8000 then 'да'
when count(p.payment_id) < 8000 then 'нет' 
end премия
from staff s 
join payment p on s.staff_id = p.staff_id 
group by s.staff_id
```

### Задание 5*

Найдите фильмы, которые ни разу не брали в аренду.

### ответ

```mysql
SELECT f.title
FROM film f
left JOIN inventory i ON i.film_id = f.film_id
left JOIN rental r ON r.inventory_id = i.inventory_id
WHERE r.rental_id IS NULL;
```

