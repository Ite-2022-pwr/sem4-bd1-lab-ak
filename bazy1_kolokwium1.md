# Bazy danych 1 - kolokwium 1

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

(2) Wyswietl wszystkie działy znajdujące się w Europie.

```sql
select * 
from departments
inner join locations
on locations.location_id = departments.location_id
inner join countries
on locations.country_id = countries.country_id
inner join regions
on regions.region_id = countries.region_id
where regions.region_name = 'Europe';
```

## Zadanie 2

(2) Wyświetl imię, nazwisko i zarobki każdego pracownika, który został
zatrudniony wcześniej niż swój manager (1). Posortuj wyniki alfabetycznie
po nazwisku (1).

```sql
select 
    e.first_name,
    e.last_name, 
    e.salary
from employees e
left join (select * from employees where employee_id in (select distinct manager_id from employees)) m
on e.manager_id = m.employee_id
where e.hire_date < m.hire_date
order by e.last_name;
```

## Zadanie 3

(2) Wyświetl wszystkie osoby, które zostały zatrudnione w ciągu 20 miesięcy (1) od założenia firmy (1). Za datę założenia firmy przyjmij datę
zatrudnienia pierwszego pracownika.

```sql
select * from employees
where months_between(
    hire_date,
    (select hire_date creation_date from employees order by 1 fetch first row only)
    ) <= 20;
```

## Zadanie 4

(3) Wyświetl osoby zarabiające najwięcej w swoim dziale (2), niebędące
managerami (1).

```sql
select * from employees
natural join
(select department_id, max(salary) salary
    from employees
    where employee_id not in (select distinct manager_id from employees where manager_id is not null)
    group by department_id);
```

## Zadanie 5

(4) Dodaj nowy dział University znajdujący się we Wrocławiu (2). Zatrudnij w nim z dniem dzisiejszym naszego rektora (1) na takim samym
stanowisku, co Steven King (1).

```sql
insert into countries values ('PL', 'Poland', (select region_id from regions where regions.region_name = 'Europe'));

insert into locations (location_id, city, country_id)
values (3300, 'Wroclaw', 'PL');

insert into departments
values (280, 'University', null, (select location_id from locations where city = 'Wroclaw'));

insert into employees (employee_id, first_name, last_name, email, job_id, department_id, hire_date)
values (207,
        'Arkadiusz',
        'Wojs',
        'AWOJS',
        (select job_id from employees where first_name = 'Steven' and last_name = 'King'),
        (select department_id from departments where department_name = 'University'),
        sysdate
        );
```

## Zadanie 6

(2) Dodaj do każdego maila pracownika końcówkę @gmail.com.

```sql
update employees
set email = email || '@gmail.com';
```

## Zadanie 7

(3) Utwórz tabelę big_departments o strukturze tabeli departments
(1), która będzie zawierała wszystkie działy mające więcej niż 3 zatrudnionych pracowników (2).

```sql
create table BIG_DEPARTMENTS as (select * from departments where department_id in (select department_id from employees group by employees.department_id having count(*) > 3));
```