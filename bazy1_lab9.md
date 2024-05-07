# Bazy danych 1 - laboratorium 9

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Stwórz widok w którym znajdą się dane wszystkich pracowników o
nazwiskach zaczynających się na S oraz pracujących w dziale zatrudniającym conajmniej 5 pracowników. Posortuj rekordy w widoku alfabetycznie.

```sql
create view employees_s_view as
select 
    *
from employees
where substr(last_name, 1, 1) = 'S'
    and department_id in (
        select 
            department_id
        from employees
        group by department_id
        having count(*) >= 5
    )
order by last_name;
```

Dodaj dowolnego pracownika do utworzonego widoku. Czy można
dodać pracownika niespełniającego warunków widoku?

```sql
insert into employees_s_view
values (207, 'Bob', 'Kowalski', 'BKOWALSKI', '650.505.3876', sysdate, 'PR_REP', 2137, null, 101, 70);
```

```
Można. Nie wystąpił błąd, jednak nowy wiersz został dodany do tabeli EMPLOYEES i nie wyświetla się w widoku.
```

Usuń utworzony widok.

```sql
drop view employees_s_view;
```

Stwórz widok ponownie, z opcją sprawdzającą warunek widoku przy
operacjach DML. Zmień nazwisko wszystkich pracowników z widoku
na ’Adams’. Czy jest to możliwe? Dlaczego tak/nie?

```sql
create or replace view employees_s_view as
select 
    *
from employees
where substr(last_name, 1, 1) = 'S'
    and department_id in (
        select 
            department_id
        from employees
        group by department_id
        having count(*) >= 5
    )
order by last_name
with check option;
```

```sql
update employees_s_view
set last_name = 'Adams';
```

```
Error report -
ORA-01402: naruszenie klauzuli WHERE dla perspektywy z WITH CHECK OPTION
```

```
Nie jest to możliwe. Klauzula WITH CHECK OPTION nie pozwala na dodawanie do widoku rekordów niespełniających warunków tego widoku.
```

## Zadanie 2

Stwórz widok z kolumnami job_title, min_salary, max_salary
z tabeli jobs dla tych stanowisk, które rzeczywiste średnie zarobki
są większe niż średnie zarobki całej firmy.

```sql
create or replace view best_jobs_view as
select
    job_title,
    min_salary,
    max_salary
from jobs
where job_id in (
    select 
        job_id
    from employees
    group by job_id
    having avg(salary) > (select avg((max_salary + min_salary) / 2) from jobs)
)
with check option;
```

Zmień nazwę stanowiska o najwyższych możliwych zarobkach na Best
job in the whole company Czy nazwa stanowiska z tabeli jobs również
uległa zmianie?

```sql
update best_jobs_view
set job_title = 'a Best job in the whole company'
where max_salary = (select max(max_salary) from best_jobs_view);
```

```
Tak, nazwa stanowiska z tabeli JOBS również uległa zmianie.
```

## Zadanie 3

Stwórz widok z kolumnami first_name i last_name z tabeli employees, department_name z tabeli departments oraz job_title
z tabeli jobs. Uwzględnij tylko pracowników zatrudnionych w 2005
roku.

```sql
create or replace view my_view as
select
    e.first_name,
    e.last_name,
    d.department_name,
    j.job_title
from employees e
inner join departments d
on e.department_id = d.department_id
inner join jobs j
on e.job_id = j.job_id
where extract(year from hire_date) = 2005
with check option;
```

Usuń wszystkich pracowników na stanowisku ’Programmer’. Czy jest
to możliwe? Czemu tak/nie?

```sql
delete from my_view
where job_title = 'Programmer';
```

```
Jest to możliwe. Z tabeli employees zostały usunięte tylko wiersze, które występowały w widoku. Czemu? Bo w sumie czemu nie.
```

Zmień nazwę działu ’Shipping’ na ’Boating’ Czy jest to możliwe?
Dlaczego tak/nie? Co oznacza określenie ’key-preserved table’?

```sql
update my_view
set department_name = 'Boating'
where department_name = 'Shipping';
```

```
Error report -
SQL Error: ORA-01779: nie można modyfikować kolumny, która odwzorowuje się do tabeli nie zachowującej kluczy
01779. 00000 -  "cannot modify a column which maps to a non key-preserved table"
*Cause:    An attempt was made to insert or update columns of a join view which
           map to a non-key-preserved table.
*Action:   Modify the underlying base tables directly.
```

```
Nie jest to możliwe, ponieważ nie można modyfikować kolumny, która mapuje do tabeli, która nie jest 'key-preserved'. Określenie 'key-preserved table' oznacza, że 1 wartość klucza trafia do 1 tabeli.
```
