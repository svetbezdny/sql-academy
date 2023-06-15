## Задание 1

*Вывести имена всех людей, которые есть в базе данных авиакомпаний*

```sql
  select name from Passenger 
```

## Задание 2

*Вывести названия всеx авиакомпаний*

```sql
  select name from Company
```

## Задание 3

*Вывести все рейсы, совершенные из Москвы*

```sql
  select * from Trip
  where town_from = 'Moscow'
```

## Задание 4

*Вывести имена людей, которые заканчиваются на "man"*

```sql
  select name from Passenger
  where name like '%man'
```

## Задание 5

*Вывести количество рейсов, совершенных на TU-134*

```sql
  select count(*) as count from Trip
  where plane = 'TU-134'
```

## Задание 6

*Какие компании совершали перелеты на Boeing*

```sql
  select distinct name from Trip t
  join Company c on t.company = c.id
  where plane = 'Boeing'
```

## Задание 7

*Вывести все названия самолётов, на которых можно улететь в Москву (Moscow)*

```sql
  select distinct plane from Trip
  where town_to = 'Moscow'
```

## Задание 8

*В какие города можно улететь из Парижа (Paris) и сколько времени это займёт?*

```sql
  select 
   town_to, 
   timediff(time_in, time_out) as flight_time
  from Trip
  where town_from = 'Paris'
```

## Задание 9

*Какие компании организуют перелеты из Владивостока (Vladivostok)?*

```sql
  select name from Company
  join Trip on Company.id = Trip.company
  where town_from = 'Vladivostok'
```

## Задание 10

*Вывести вылеты, совершенные с 10 ч. по 14 ч. 1 января 1900 г.*

```sql
  select * from Trip
  where time_out between '1900-01-01 10:00' and '1900-01-01 14:00'
```

## Задание 11

*Вывести пассажиров с самым длинным именем*

```sql
  select name from Passenger
  where length(name) = (
    select max(length(name))
	from Passenger
)
```

## Задание 12

*Вывести id и количество пассажиров для всех прошедших полётов*

```sql
  select 
   trip,
   count(*) as count
  from Trip
  join Pass_in_trip on Trip.id = Pass_in_trip.trip
  group by 1
```

## Задание 13

*Вывести имена людей, у которых есть полный тёзка среди пассажиров*

```sql
  select name
  from (
    select 
	 name,
     row_number() over (partition by name) as rn
    from passenger
  ) t
  where rn > 1
```

## Задание 14

*В какие города летал Bruce Willis*

```sql
  select distinct town_to
  from Trip t
  join Pass_in_trip pit on t.id = pit.trip
  join Passenger p on pit.passenger = p.id
  where p.name = 'Bruce Willis'
```

## Задание 15

*Выведите дату и время прилёта пассажира Стив Мартин (Steve Martin) в Лондон (London)*

```sql
  select time_in
  from Trip t
  join Pass_in_trip pit on t.id = pit.trip
  join Passenger p on pit.passenger = p.id
  where p.name = 'Steve Martin'
    and town_to = 'London'
```

## Задание 16

*Вывести отсортированный по количеству перелетов (по убыванию) и имени (по возрастанию) список пассажиров, совершивших хотя бы 1 полет.*

```sql
  select 
   name,
   count(*) as count
  from Passenger p
  join Pass_in_trip pit on p.id = pit.passenger
  join Trip t on pit.trip = t.id
  group by name
  having count(*) >= 1
  order by count desc, name
```

## Задание 17

*Определить, сколько потратил в 2005 году каждый из членов семьи. В результирующей выборке не выводите тех членов семьи, которые ничего не потратили.*

```sql
  select 
   member_name,
   status,
   sum(amount * unit_price) as costs
  from FamilyMembers fm
  join Payments p on fm.member_id = p.family_member
  where extract(year from date) = 2005
  group by 1, 2
```

## Задание 18

*Узнать, кто старше всех в семьe*

```sql
  select member_name
  from FamilyMembers
  order by birthday
  limit 1
```

## Задание 19

*Определить, кто из членов семьи покупал картошку (potato)*

```sql
  select distinct status
  from FamilyMembers fm
  join Payments p on fm.member_id = p.family_member
  where p.good in (
    select good_id from Goods where good_name = 'potato'
  )
```

## Задание 20

*Сколько и кто из семьи потратил на развлечения (entertainment). Вывести статус в семье, имя, сумму*

```sql
  select 
   status,
   member_name,
   sum(amount * unit_price) as costs
  from FamilyMembers fm
  join Payments p on fm.member_id = p.family_member
  join Goods g on p.good = g.good_id
  join GoodTypes gt on g.type = gt.good_type_id
  where good_type_name = 'entertainment'
  group by status, member_name
```

## Задание 21

*Определить товары, которые покупали более 1 раза*

```sql
  select distinct good_name from Goods g
  where good_id in (
    select good from Payments
    group by good
    having count(*) > 1
  )
```

## Задание 22

*Найти имена всех матерей (mother)*

```sql
  select member_name from FamilyMembers
  where status = 'mother'
```

## Задание 23

*Найдите самый дорогой деликатес (delicacies) и выведите его цену*

```sql
  select 
   good_name,
   unit_price
  from Goods g
  join Payments p on g.good_id = p.good
  where type in (
    select good_type_id from GoodTypes
    where good_type_name = 'delicacies'
  )
  order by 2 desc
  limit 1
```

## Задание 24

*Определить кто и сколько потратил в июне 2005*

```sql
  select 
   member_name,
   sum(amount * unit_price) as costs
  from FamilyMembers fm
  join Payments p on fm.member_id = p.family_member
  where month(date) = 6
   and year(date) = 2005
  group by member_name
```

## Задание 25

*Определить, какие товары не покупались в 2005 году*

```sql
  select good_name from Goods g
  where good_id not in (
    select good from Payments where year(date) = 2005
	)
```

## Задание 26

*Определить группы товаров, которые не приобретались в 2005 году*

```sql
  select good_type_name
  from GoodTypes gt
  left join Goods g on gt.good_type_id = g.type
  left join Payments p on g.good_id = p.good
  group by 1
  having count(payment_id) = 0
```

## Задание 27

*Узнать, сколько потрачено на каждую из групп товаров в 2005 году. Вывести название группы и сумму*

```sql
  select good_type_name,
   sum(amount * unit_price) as costs
  from GoodTypes gt
  join Goods g on gt.good_type_id = g.type
  join Payments p on g.good_id = p.good
  where year(date) = 2005
  group by 1
```

## Задание 28

*Сколько рейсов совершили авиакомпании из Ростова (Rostov) в Москву (Moscow) ?*

```sql
select count(*) as count
from Trip
where town_from = 'Rostov'
  and town_to = 'Moscow'
```

## Задание 29

*Выведите имена пассажиров улетевших в Москву (Moscow) на самолете TU-134*

```sql
select distinct name
from Passenger p
join Pass_in_trip pit on p.id = pit.passenger
join Trip t on pit.trip = t.id
where town_to = 'Moscow'
and plane = 'TU-134'
```

## Задание 30

*Выведите нагруженность (число пассажиров) каждого рейса (trip). Результат вывести в отсортированном виде по убыванию нагруженности.*

```sql
select trip, count(*) as count
from Trip t
join Pass_in_trip pit on t.id = pit.trip
group by 1
order by 2 desc
```

## Задание 31

*Вывести всех членов семьи с фамилией Quincey.*

```sql
select * from FamilyMembers
where member_name like '%Quincey'
```

## Задание 32

*Вывести средний возраст людей (в годах), хранящихся в базе данных. Результат округлите до целого в меньшую сторону.*

```sql
select floor(avg(year(curdate()) - year(curdate))) as age from FamilyMembers
```

## Задание 33

*Найдите среднюю стоимость икры. В базе данных хранятся данные о покупках красной (red caviar) и черной икры (black caviar).*

```sql
select avg(unit_price) as cost
from Payments p
join Goods g on p.good = g.good_id
where good_name in ('red caviar', 'black caviar')
```

## Задание 34

*Сколько всего 10-ых классов*

```sql
select count(*) as count from Class
where name like '10%'
```

## Задание 35

*Сколько различных кабинетов школы использовались 2.09.2019 в образовательных целях ?*

```sql
select count(distinct id) as count
from Schedule
where date = '2019-09-02'
```

## Задание 36

*Выведите информацию об обучающихся живущих на улице Пушкина (ul. Pushkina)?*

```sql
select * from Student
where address like 'ul. Pushkina%'
```

## Задание 37

*Сколько лет самому молодому обучающемуся ?*

```sql
select min(timestampdiff(year, birthday, curdate())) as year from Student 
```

## Задание 38

*Сколько Анн (Anna) учится в школе ?*

```sql
select count(*) as count from Student
where first_name = 'Anna'
```

## Задание 39

*Сколько обучающихся в 10 B классе ?*

```sql
select count(*) as count
from Student_in_class
where class in (
  select id from Class where name = '10 B'
)
```

## Задание 40

*Выведите название предметов, которые преподает Ромашкин П.П. (Romashkin P.P.) ?*

```sql
select distinct name as subjects
from Subject su
join Schedule sc on su.id = sc.subject
join Teacher t on sc.teacher = t.id
where concat(
  last_name, ' ',
  left(first_name, 1), '.',
  left(middle_name, 1), '.'
) = 'Romashkin P.P.'
```

## Задание 41

*Во сколько начинается 4-ый учебный предмет по расписанию ?*

```sql
select start_pair from Timepair t where id = 4
```

## Задание 42

*Сколько времени обучающийся будет находиться в школе, учась со 2-го по 4-ый уч. предмет ?*

```sql
select timediff(max(end_pair), min(start_pair)) as time
from Timepair
where id between 2 and 4
```

## Задание 43

*Выведите фамилии преподавателей, которые ведут физическую культуру (Physical Culture). Отcортируйте преподавателей по фамилии.*

```sql
select last_name
from Teacher t
join Schedule s on t.id = s.teacher
join Subject su on s.subject = su.id
where name = 'Physical Culture'
order by 1
```

## Задание 44

*Найдите максимальный возраст (колич. лет) среди обучающихся 10 классов ?*

```sql
select max(timestampdiff(year, birthday, curdate())) as max_year
from Student s
join Student_in_class sic on s.id = sic.student
join Class c on sic.class = c.id
where name like '10%'
```

## Задание 45

*Какие кабинеты чаще всего использовались для проведения занятий? Выведите те, которые использовались максимальное количество раз.*

```sql
select classroom
from (
  select classroom, dense_rank() over(order by count(*) desc
  ) r
from Schedule
group by 1
) t where r = 1
```

## Задание 46

*В каких классах введет занятия преподаватель "Krauze" ?*

```sql
select distinct name
from Class c
join Schedule s on c.id = s.class
join Teacher t on s.teacher = t.id
where last_name = 'Krauze'
```

## Задание 47

*Сколько занятий провел Krauze 30 августа 2019 г.?*

```sql
select count(*) as count
from Schedule s
join Teacher t on s.teacher = t.id
where last_name = 'Krauze'
and date = '2019-08-30'
```

## Задание 48

*Выведите заполненность классов в порядке убывания*

```sql
select name, count(*) as count
from Class c
join Student_in_class sic on c.id = sic.class
group by 1
order by 2 desc
```

## Задание 49

*Какой процент обучающихся учится в 10 A классе ?*

```sql
select 100 * sum(case when name = '10 A' then 1 else 0 end ) / count(*) as percent
from Student_in_class sic
join Class c on sic.class = c.id
```

## Задание 50

*Какой процент обучающихся родился в 2000 году? Результат округлить до целого в меньшую сторону.*

```sql
select floor(100 * sum(case when year(birthday) = 2000 then 1 else 0 end) / count(*)) as percent
from student
```

## Задание 51

*Добавьте товар с именем "Cheese" и типом "food" в список товаров (Goods).*

```sql
insert into Goods
select 
 count(*) + 1 as good_id,
 'Cheese' as good_name,
 (
  select good_type_id from GoodTypes where good_type_name = 'food'
 ) as type
from Goods
```

## Задание 52

*Добавьте в список типов товаров (GoodTypes) новый тип "auto".*

```sql
insert into GoodTypes
select 
 count(*) + 1 as good_type_id,
 'auto' as good_type_name
from GoodTypes
```

## Задание 53

*Измените имя "Andie Quincey" на новое "Andie Anthony".*

```sql
update FamilyMembers
set member_name = 'Andie Anthony'
where member_name = 'Andie Quincey'
```

## Задание 54

*Удалить всех членов семьи с фамилией "Quincey".*

```sql
delete from FamilyMembers
where member_name like '%Quincey'
```

## Задание 55

*Удалить компании, совершившие наименьшее количество рейсов.*

```sql
delete from Company
where id in (
 select company from (
  select 
   company,
   dense_rank() over(order by count(*)) as dr
  from Trip
  group by 1
 ) t
 where dr = 1)
```

## Задание 56

*Удалить все перелеты, совершенные из Москвы (Moscow).*

```sql
delete from Trip where town_from = 'Moscow'
```

## Задание 57

*Перенести расписание всех занятий на 30 мин. вперед.*

```sql
update Timepair
set start_pair = date_add(start_pair, interval 30 minute),
    end_pair = date_add(end_pair, interval 30 minute)
```

## Задание 58

*Добавить отзыв с рейтингом 5 на жилье, находящиеся по адресу "11218, Friel Place, New York", от имени "George Clooney"*

```sql
insert into Reviews
 select 
  count(*) + 1 as id,
  (
   select Reservations.id from Reservations
   join Users on Reservations.user_id = Users.id
   join Rooms on Reservations.room_id = Rooms.id
   where name = 'George Clooney' and address = '11218, Friel Place, New York'
  ) as reservation_id,
 5 as rating
from Reviews
```

## Задание 59

*Вывести пользователей,указавших Белорусский номер телефона ? Телефонный код Белоруссии +375.*

```sql
select * from Users
where phone_number like '+375%'
```

## Задание 60

*Выведите идентификаторы преподавателей, которые хотя бы один раз за всё время преподавали в каждом из одиннадцатых классов.*

```sql
select teacher
from Teacher
join Schedule on Teacher.id = Schedule.teacher
join Class on Schedule.class = Class.id
where Class.name like '11%'
group by 1
having count(distinct class) = 
 (
  select count(*)from Class where name like '11%'
 )
```

## Задание 61

*Выведите список комнат, которые были зарезервированы в течение 12 недели 2020 года.*

```sql
select Rooms.* from Rooms
join Reservations on Rooms.id = Reservations.room_id
where weekofyear(start_date) = 12
```

## Задание 62

*Вывести в порядке убывания популярности доменные имена 2-го уровня, используемые пользователями для электронной почты. Полученный результат необходимо дополнительно отсортировать по возрастанию названий доменных имён.*

```sql
select 
 substring_index(email, '@', -1) as domain,
 count(*) as count
from Users
group by 1
order by 2 desc, 1
```

## Задание 63

*Выведите отсортированный список (по возрастанию) фамилий и имен студентов в виде Фамилия.И.*

```sql
select concat(last_name, '.', left(first_name, 1), '.') as name
from Student
order by last_name, first_name
```

## Задание 64

*Вывести количество бронирований по каждому месяцу каждого года, в которых было хотя бы 1 бронирование. Результат отсортируйте в порядке возрастания даты бронирования.*

```sql
select 
 year(start_date) as year,
 month(start_date) as month,
 count(*) as amount
from Reservations
group by 1, 2
order by 1, 2
```

## Задание 65

*Необходимо вывести рейтинг для комнат, которые хоть раз арендовали, как среднее значение рейтинга отзывов округленное до целого вниз.*

```sql
select room_id, floor(avg(rating)) as rating
from Reservations
join Reviews on Reservations.id = Reviews.reservation_id
group by 1
```

## Задание 66

*Вывести список комнат со всеми удобствами (наличие ТВ, интернета, кухни и кондиционера), а также общее количество дней и сумму за все дни аренды каждой из таких комнат.*

```sql
select 
 home_type,
 address,
 coalesce(sum(datediff(end_date, start_date)), 0) as days,
 coalesce(sum(total), 0) as total_fee
from Rooms
left join Reservations on Rooms.id = Reservations.room_id
where 1 = 1
 and has_tv = 1
 and has_internet = 1
 and has_kitchen = 1
 and has_air_con = 1
group by 1, 2
```

## Задание 67

*Вывести время отлета и время прилета для каждого перелета в формате "ЧЧ:ММ, ДД.ММ - ЧЧ:ММ, ДД.ММ", где часы и минуты с ведущим нулем, а день и месяц без.*

```sql
select concat(
 date_format(time_out, '%H:%i'), ', ',
 cast(day(time_out) as unsigned), '.',
 cast(month(time_out) as unsigned), ' - ',
 date_format(time_in, '%H:%i'), ', ',
 cast(day(time_in) as unsigned), '.',
 cast(month(time_in) as unsigned) 
) as flight_time
from Trip
```

## Задание 68

*Для каждой комнаты, которую снимали как минимум 1 раз, найдите имя человека, снимавшего ее последний раз, и дату, когда он выехал*

```sql
select distinct room_id,
 last_value(name) over w as name,
 last_value(end_date) over w as end_date
from Reservations
join Users on Reservations.user_id = Users.id 
window w as (partition by room_id order by start_date rows between unbounded preceding and unbounded following) 
```

## Задание 69

*Вывести идентификаторы всех владельцев комнат, что размещены на сервисе бронирования жилья и сумму, которую они заработали*

```sql
select owner_id,
 coalesce(sum(total), 0) as total_earn
from Rooms
left join Reservations on Rooms.id = Reservations.room_id
group by 1
```

## Задание 70

*Необходимо категоризовать жилье на economy, comfort, premium по цене соответственно <= 100, 100 < цена < 200, >= 200. В качестве результата вывести таблицу с названием категории и количеством жилья, попадающего в данную категорию*

```sql
select 
 case when price <= 100 then 'economy' 
  when 100 < price and price < 200 then 'comfort'
  when price >= 200 then 'premium' end as category,
 count(*) as count
from Rooms
group by 1
```

## Задание 71

*Найдите какой процент пользователей, зарегистрированных на сервисе бронирования, хоть раз арендовали или сдавали в аренду жилье. Результат округлите до сотых.*

```sql
select round(100 * count(t.id) / count(Users.id), 2) as percent
from Users
left join (
 select user_id as id from Reservations
  union
 select owner_id as id from Rooms
 join Reservations on Rooms.id = Reservations.room_id
) t on t.id = Users.id
```

## Задание 72

*Выведите среднюю стоимость бронирования для комнат, которых бронировали хотя бы один раз. Среднюю стоимость необходимо округлить до целого значения вверх.*

```sql
select 
 room_id,
 ceil(avg(Reservations.price)) as avg_price
from Rooms
join Reservations on Rooms.id = Reservations.room_id
group by 1
```

## Задание 73

*Выведите id тех комнат, которые арендовали нечетное количество раз*

```sql
select room_id,
 count(*) as count
from Reservations
group by 1
having count(*) % 2 != 0
```

## Задание 74

*Выведите идентификатор и признак наличия интернета в помещении. Если интернет в сдаваемом жилье присутствует, то выведите «YES», иначе «NO».*

```sql
select 
 id,
 case has_internet when 1 then 'YES' else 'NO' end as has_internet
from Rooms
```

## Задание 75

*Выведите фамилию, имя и дату рождения студентов, кто был рожден в мае.*

```sql
select 
 last_name,
 first_name,
 birthday
from Student
where month(birthday) = 5
```

## Задание 76

*Вывести имена всех пользователей сервиса бронирования жилья, а также два признака: является ли пользователь собственником какого-либо жилья (is_owner) и является ли пользователь арендатором (is_tenant). В случае наличия у пользователя признака необходимо вывести в соответствующее поле 1, иначе 0.*

```sql
select 
 name,
 case when owner_id is null then 0 else 1 end as is_owner,
 case when user_id is null then 0 else 1 end as is_tenant
from Users
left join (
 select distinct user_id from Reservations
) Reservations on Users.id = Reservations.user_id
left join (
 select distinct owner_id from Rooms
) Rooms on Users.id = Rooms.owner_id
```