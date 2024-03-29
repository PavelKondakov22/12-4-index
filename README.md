# 12-4-index Кондаков Павел
### Задание 1  

Ответ:   
```
select ROUND(SUM(index_length)*100/SUM(data_length)) 'size index in %' from INFORMATION_SCHEMA.TABLES;

```
![alt text](https://github.com/PavelKondakov22/12-4-index/blob/main/s1.png)  
Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

### Задание 2

Выполните explain analyze следующего запроса:
```sql
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```
- перечислите узкие места;
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.
- Ответ:   

![alt text](https://github.com/PavelKondakov22/12-4-index/blob/main/s2.png) 

Как я понял, узким местом данного запроса является то, что запрос обрабатывает излишние таблицы а именно inventory, rental и film. Все необходимые данные есть в таблицах payment и customer, соответственно, остальные таблицы можно исключить.
Так же предлагаю добавить индекс:
```
CREATE index payment_date on payment(payment_date);
```
Изменим запрос:
```
select concat(c.last_name, ' ', c.first_name), sum(p.amount)
from payment p, customer c
inner join rental r on r.rental_date = p.payment_date
inner join customer c on c.customer_id = r.customer_id 
where p.payment_date between '2005-07-30 00:00:00' and '2005-07-30 23:59:59'
group by c.last_name, c.first_name, c.customer_id;
```
![alt text](https://github.com/PavelKondakov22/12-4-index/blob/main/s3.png) 
