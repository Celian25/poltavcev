# Ответы на задачи из тестов Полтавцева по Языкам манипулирования данными

1.Выбрать всех заказчиков чьи имена попали в алфавитный диапазон 'A' - 'G'

```sql
select cname from customers
where cname between 'A' and 'H';
```

2.Найти всех заказчиков чьи имена  начинаются  с  'G'

```sql
select cname from customers
where cname like 'g%';
```

3.Найти все записи таблице Заказчиков  с  NULL  значениями  в столбце city

```sql
select * from customers
where city is null
```

4.Найти сумму заказов  в  таблице  Заказов

```sql
select SUM(amt) as amt
from orders;
```

5.Найти наибольшую сумму приобретений (amt) полученную каждым продавцом.

```sql
select salespeople.sname, sum(orders.amt) as total_amt from salespeople
join orders
on salespeople.snum = orders.snum
group by salespeople.sname
order by total_amt desc
```

6.Найти наибольшую  сумму   приобретений (amt)  получаемую   каждым продавцом  каждый  день 

```sql
select snum,sum(amt) as daily_amt, odate from orders
group by odate, snum
```

7.Найти  максимальную  стоимость  приобретений (amt) свыше $3000.00

```sql
select max(amt) from orders
where amt > 3000
```

8.Найти  наибольшие заказы продавцов Serres и Rifkin

```sql
select sname, MAX(amt) as max_amt from salespeople
join orders on salespeople.snum = orders.snum
where sname = 'Serres' or sname = 'Rifkin'
group by sname
```

9.Выбрать данные из таблицы   Продавцов, упорядочив в порядке убывания к  наименьшему  значению  комиссионных (comm)

```sql
select * from salespeople
order by comm desc
```

10.Подсчитать количество заказов  каждого  из  продавцов, и вывести результаты в убывающем порядке

```sql
select sname, COUNT(*) as total from salespeople
join orders on salespeople.snum = orders.snum
group by sname
order by total desc
#используем count(*) Для подсчета всех полей таблицы 
```

11.Вывести пары продавцов - заказчиков живущих в одном городе

```sql
select salespeople.sname, customers.cname from salespeople
join customers
on salespeople.city = customers.city
```

12.Вывести для каждого заказчика имя продавца который его обслуживает

```sql
select sname, cname from salespeople
join customers on salespeople.snum = customers.snum
```

13.Вывести все пары заказчиков имеющих один и тот же рейтинг (rating)

```sql
select * from customers c1
cross join customers c2
where c1.rating = c2.rating   
and c1.cnum < c2.cnum
```

14.Вывести все пары заказчиков: один с рейтингом (rating) 100, второй 200

```sql
select * from customers c1
cross join customers c2
where c1.rating = 100  
and c2.rating = 200 
and c1.cnum < c2.cnum
```

15.Вывести все тройки заказчиков: один с рейтингом (rating) 100, второй 200, третий 300

```sql
select * from customers c1
cross join customers c2
cross join customers c3
where c1.rating = 100 
and c2.rating = 200
and c3.rating = 300
and c1.cnum < c2.cnum
and c2.cnum < c3.cnum
```

16.Вывести из таблицы Заказов все  заказы продавца Motika

```sql
select orders.* from orders #запрос со звездочкой когда хотим вывести только поля из таблицы orders
join salespeople on orders.snum = salespeople.snum
where sname = 'Motika'
```

17.Вывести все заказы продавца который обслуживает Hoffmanа .

```sql
select * from orders 
where snum in (select salespeople.snum from salespeople
join customers
on salespeople.snum = customers.snum
where cname = 'Hoffman') #используем in чтобы работало при нескольких snum'ах, а в подзапросе ищем заказы на hoffman'а
```

18.Вывести все  заказы  имеющие сумму приобретений (amt) выше средней на 4-е  Октября

```sql
select * from orders
where amt > (select AVG(amt) from orders)
and MONTH(odate) = 10 and day(odate)=4
```

19.Найти среднее значение комиссионных продавцов в Лондоне

```sql
select AVG(comm) from salespeople
where city = 'London'
```

20.Вывести все строки таблицы Заказов для продавцов в Лондоне

```sql
select orders.* from orders
join salespeople on orders.snum = salespeople.snum
where salespeople.city = 'London'
```

21.Вывести  комиссионные  всех  продавцов  обслуживающих заказчиков в Лондоне

```sql
select distinct salespeople.sname, salespeople.comm from salespeople
join customers
on salespeople.snum = customers.snum
where customers.city = 'London' #используем distinct, т.к. две одинаковые записи
```

22.Подсчитатьт количество заказчиков в  San Jose с оценками  (rating) выше среднего

```sql
select COUNT(cname) from customers
where rating > (select avg(rating) from customers) 
and city = 'San Jose'
```

23.Вывести имена и номера всех продавцов которые имеют более одного заказчика

```sql
select * from salespeople
where snum in (select snum from customers
group by snum
having COUNT(snum) > 1)
#тут чтобы подсчитать количество продавцов обслуживающих >1 заказчика, используем подзапрос с count,
#но так как в where не дает,
#группируем и применяем в having
```

24.Найти все пары заказчиков обслуживаемых одним продавцом.

```sql
select c1.cname as customer1, c2.cname as customer2 from customers c1
join customers c2
on c1.snum = c2.snum
where c1.cnum < c2.cnum
#используем условие where, чтобы показать только уникальные записи 
#(пересечение из теории множеств)
```

25.Найти всех заказчиков размещенных в городах где продавец Serres ( snum 1002 ) имеет заказчиков:

```sql
select * from customers
where customers.city in (select city from customers where snum =1002)
```

26.Найти всех продавцов, проживающих в Барселоне или Лондоне (ответ: 1001,1004,1007)

```sql
select * from salespeople 
where city = 'London' or city = 'Barcelona'
```

27.Выбрать заказы в которых или сумма заказа > 2000.00 или  сделанные 03-10-1990 продавцами с номерами  > 1002

```sql
select * from orders
where amt > 2000 or
(YEAR(odate) = 1990 and MONTH(odate) = 10 and DAY(odate) = 3 
and snum > 1002)
```

28.Выбрать заказы для которых НЕ выполняются следующие условия:
или сумма заказа > 2000.00 или  сделаны 03-10-1990 продавцами с номерами  > 1002

```sql
select * from orders
where not (amt > 2000 or
(YEAR(odate) = 1990 and MONTH(odate) = 10 and DAY(odate) = 3 
and snum > 1002))
```

29.Выбрать заказчиков. сделавших заказы 3 октября 1990 года

```sql
select customers.cname from customers
join orders on
customers.cnum = orders.cnum
where (MONTH(odate) = 10 and DAY(odate) = 3 and YEAR(odate) = 1990)
```

30.Найти продавцов заказчики которых имеют более чем один заказ.

```sql
select distinct salespeople.sname from salespeople
join customers on
salespeople.snum = customers.snum
where customers.cnum in (select customers.cnum from customers
join orders on
customers.cnum = orders.cnum
group by customers.cnum
having COUNT(customers.cnum) > 1)
```
