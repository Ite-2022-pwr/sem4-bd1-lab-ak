# Bazy danych 1 - laboratorium 4

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Dla każdego działu wypisz jego nazwę oraz miasto, kraj i region w jakim
się znajduje.

```sql
select
    departments.department_name,
    locations.city,
    countries.country_name,
    regions.region_name
from departments
join locations on locations.location_id = departments.location_id
join countries on locations.country_id = countries.country_id
join regions on countries.region_id = regions.region_id
```

## Zadanie 2

Wypisz imię, nazwisko oraz stanowisko każdego pracownika, którego wypłata różni się co najwyżej o 500 od minimalnej lub maksymalnej zakładanej wypłaty na danych stanowiskach.

```sql
select
    employees.first_name,
    employees.last_name,
    jobs.job_title
from employees
left join jobs on employees.job_id = jobs.job_id
where
abs(employees.salary - jobs.min_salary) < 500
or
abs(jobs.max_salary - employees.salary) < 500
```

## Zadanie 3

Dla każdego pracownika wypisz w jednej kolumnie jego imię i nazwisko,
a w drugiej imię i nazwisko jego managera. Nie wyświetlaj pracowników,
do których nie jest przypisany manager. Nie używaj wyrażenia WHERE.

```sql
select
    employees.first_name || ' ' ||  employees.last_name as pracownik,
    managers.first_name || ' ' ||  managers.last_name as manager
from employees
inner join employees managers on employees.manager_id = managers.employee_id
```

## Zadanie 4

Wypisz imiona, nazwiska, historyczne nazwy stanowisk i działów osób,
które zakończyły pracę na danym stanowisku przed 01.01.2007.

```sql
select
    employees.first_name,
    employees.last_name,
    jobs.job_title,
    departments.department_name
from employees
inner join job_history on job_history.employee_id = employees.employee_id
inner join jobs on jobs.job_id = job_history.job_id
inner join departments on job_history.department_id = departments.department_id
where job_history.end_date < to_date('01.01.2007', 'dd.MM.yyyy')
```
## Zadanie 5

Wyświetl pracowników, którzy nie są zapisani w historii zatrudnień.

```sql
select
    employees.first_name,
    employees.last_name
from employees
left join job_history on job_history.employee_id = employees.employee_id
where job_history.job_id is null
```

## Zadanie 6

Wyświetl nazwę każdego działu, a także minimalną i maksymalną wypłatę
zatrudnionych w nim pracowników. Nie wyświetlaj rekordów w których
któraś z wartości jest pusta. Nie używaj wyrażenia WHERE.

```sql
select
    departments.department_name,
    max(employees.salary) as "najwieksza wyplata",
    min(employees.salary) as "najmniejsza wyplata"
from departments
inner join employees on employees.department_id = departments.department_id
group by departments.department_name
```