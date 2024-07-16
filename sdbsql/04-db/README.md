# Домашнее задание к занятию «SQL. Часть 1»

### Задание 1

Получите уникальные названия районов из таблицы с адресами, которые начинаются на “K” и заканчиваются на “a” и не содержат пробелов.

### ответ
```mysql
select district from address a 
where district like 'K%a'
and district not like '% %'
```

### Задание 2

Получите из таблицы платежей за прокат фильмов информацию по платежам, которые выполнялись в промежуток с 15 июня 2005 года по 18 июня 2005 года **включительно** и стоимость которых превышает 10.00.

### ответ
```mysql
select amount,cast(payment_date as date) 
from payment
where payment_date 
between '2005-06-15' and '2005-06-19'
and amount > 10
order by payment_date asc 
```

![alt text](https://github.com/Kovrei/home_work/blob/main/sdbsql/04-db/img/12-03-02.PNG?raw=true)

### Задание 3

Получите последние пять аренд фильмов.

### ответ
```mysql
select f.title, r.rental_date from film f 
join inventory i on i.film_id = f.film_id 
join rental r on r.inventory_id = i.inventory_id 
ORDER BY rental_date DESC LIMIT 5
```

### Задание 4

Одним запросом получите активных покупателей, имена которых Kelly или Willie. 

Сформируйте вывод в результат таким образом:
- все буквы в фамилии и имени из верхнего регистра переведите в нижний регистр,
- замените буквы 'll' в именах на 'pp'.

### ответ
```mysql
select lower(replace(first_name, 'LL', 'PP')), lower (last_name)
from customer c  
where c.first_name like 'Kelly' 
or first_name like 'Willie'
```

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 5*

Выведите Email каждого покупателя, разделив значение Email на две отдельных колонки: в первой колонке должно быть значение, указанное до @, во второй — значение, указанное после @.

### ответ
```mysql
select substring_index(email,'@',1) as name,
substring_index(email,'@',-1) as site
from customer c 
```

### Задание 6*

Доработайте запрос из предыдущего задания, скорректируйте значения в новых колонках: первая буква должна быть заглавной, остальные — строчными.

### ответ
```mysql
select
	CONCAT(
		LEFT(UPPER(SUBSTRING_INDEX(c.email, '@', 1)), 1), 
		SUBSTRING(LOWER(SUBSTRING_INDEX(c.email, '@', 1)), 2)
	) as "left",
	CONCAT(
		LEFT(UPPER(SUBSTRING_INDEX(c.email, '@', -1)), 1), 
		SUBSTRING(LOWER(SUBSTRING_INDEX(c.email, '@', -1)), 2)
	) as "right"
from
	sakila.customer c;
```
