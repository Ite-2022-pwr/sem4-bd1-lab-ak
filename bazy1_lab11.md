# Bazy danych 1 - laboratorium 11

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Dla każdego managera wyświetl jego imię i nazwisko (w jednej kolumnie)
a także liczbę zatrudnionych "pod nim" pracowników w poszczególnych
latach (w których pracownicy byli zatrudniani u danego managera). W
tym samym podzapytaniu dodaj podsumowanie ogólnej liczby pracowników zatrudnionych u danego managera, liczbę pracowników zatrudnionych
w poszczególnych latach sumarycznie u wszystkich managerów, a także
liczbę pracowników w całej firmie.

```sql
select
    coalesce(m.manager_name, 'All managers') as "Manager",
    coalesce(to_char(extract(year from e.hire_date)), 'All years') as Year,
    count(*) "Employees under manager"
from employees e
inner join (select employee_id, first_name || ' ' || last_name manager_name from employees where employee_id in (select manager_id from employees)) m
on m.employee_id = e.manager_id
group by cube(m.manager_name, extract(year from e.hire_date))
```


## Zadanie 2

Dla każdego działu wypisz maksymalną, minimalną oraz sumę wypłat osób
w nim zatrudnionych. Nie bierz pod uwagę działów z mniej niż 2 pracownikami. Wyświetl również podsumowanie powyższych statystyk dla wszystkich spełniających warunek działów łącznie.

```sql
select
    coalesce(to_char(e.department_id), 'All departments') "department",
    min(salary) "min salary",
    max(salary) "max salary",
    sum(salary) "sum salary"
from employees e
where e.department_id in (select department_id from employees group by department_id having count(*) > 1)
group by rollup(e.department_id)
```

## Zadanie 3

Wypisz liczbę pracowników zatrudnionych w każdym dziale na poszczególnych stanowiskach, a także liczbę pracowników w dziale ogółem.

```sql
select
    coalesce(to_char(e.department_id), 'All departments') "Department",
    coalesce(to_char(e.job_id), 'All jobs') "Job",
    count(*) "employees"
from employees e
where e.department_id is not null
group by rollup(e.department_id, e.job_id)
```

