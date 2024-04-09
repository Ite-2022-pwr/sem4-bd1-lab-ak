# Bazy danych 1 - laboratorium 5

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Wyświetl każdego managera oraz jego managera, a także liczbę pracowników do niego przypisanych

```sql
select
    employees.first_name, 
    employees.last_name,
    man.first_name,
    man.last_name,
    cnt."Liczba pracowników"
from employees
left join (select employee_id, first_name, last_name from employees where employee_id in (select manager_id from employees)) man
on employees.manager_id = man.employee_id
left join (select employees.manager_id, count(employees.employee_id) as "Liczba pracowników" from employees group by employees.manager_id) cnt
on employees.employee_id = cnt.manager_id
where employees.employee_id in (select manager_id from employees)
order by man.first_name desc, cnt."Liczba pracowników"
```

## Zadanie 2

Wyświetl imię, nazwisko oraz wynagrodzenie każdego pracownika, którego
wynagrodzenie wynosi więcej, niż wynagrodzenie osoby z jego działu o
najkrótszym stażu. Jeśli więcej niż jedna osoba ma najkrótszy staż, to
weź pod uwagę średnią ich zarobków.

```sql
select
    employees.first_name, 
    employees.last_name,
    employees.salary
from employees
left join (
    select
        department_id,
        avg(salary) as avg_salary
    from employees
    where hire_date in (select max(hire_date) from employees group by department_id)
    group by department_id
) max_hire_date
on employees.department_id = max_hire_date.department_id
where employees.salary > max_hire_date.avg_salary
```

## Zadanie 3

Wyświetl dane obecnych pracowników, którzy zmieniali stanowisko najwięcej razy.

```sql
select
    *
from employees
where employee_id in (
    select
        employee_id
    from job_history
    group by employee_id
    having count(job_id) in (
        select
            max(count(job_id))
        from job_history
        group by employee_id
    )
)
```

## Zadanie 4

Wyświetl imię i nazwisko osób zatrudnionych na takim samym stanowisku
co Eleni Zlotkey.

```sql
select
    employees.first_name,
    employees.last_name
from employees
where employees.job_id in (
select
    job_id 
from employees
where
    first_name = 'Eleni'
    and
    last_name = 'Zlotkey'
)
```

