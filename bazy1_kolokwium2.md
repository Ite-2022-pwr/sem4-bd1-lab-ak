# Bazy danych 1 - kolokwium 2

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

(4) Stwórz widok zawierający wszystkie znajdujące się w firmie stanowiska
(id oraz nazwy) (1), wraz z rzeczywistymi minimalnymi oraz maksymalnymi zarobkami (1). Uwzględnij stanowiska, które nie mają zatrudnionych
pracowników (1). Posortuj wyniki rosnąco po liczbie zatrudnionych na
stanowiskach pracownikach (1).

```sql
create or replace view v_employees_count as
select
    j.job_id,
    j.job_title,
    count(e.employee_id) emplcount,
    min(e.salary) min_salary,
    max(e.salary) max_salary
from employees e
right join jobs j
on e.job_id = j.job_id
group by j.job_id, j.job_title
order by emplcount;
```

## Zadanie 2

(2) Stwórz indeks zawierający imię, nazwisko oraz id działu każdego pracownika (1). Napisz zapytanie, które skorzysta ze stworzonego indeksu
(1).

```sql
create index idx_empl_dep
on employees (first_name, last_name, department_id);
```

```sql
select * from employees where department_id = 90;
```

## Zadanie 3

(3) (jedna kwerenda) Dla każdego kraju i działu wypisz liczbę zatrudnionych pracowników, a także sumaryczną liczbę pracowników zatrudnionych
w danym kraju (wymagane pola: country_name, department_name,
liczba_pracowników) (2). Nie uwzględniaj krajów bez działów, ale
uwzględnij działy bez pracowników (1). Można użyć location_id zamiast nazwy kraju za -1 pkt.,

```sql
select
    coalesce(c.country_name, 'Wszystkie kraje') as country_name,
    coalesce(d.department_name, 'Wszystkie dzialy') as department_name,
    count(e.employee_id) as liczba_pracownikow
from departments d
left join employees e
    on e.department_id = d.department_id
inner join locations l
    on d.location_id = l.location_id
inner join countries c
    on l.country_id = c.country_id
group by
    rollup(c.country_name, d.department_name);
```

## Zadanie 4

(2) (PL\SQL) Napisz funkcję (1) wyliczającą różnicę między średnią dwóch
pierwszych liczb, a trzecią liczbą (1).

```sql
create or replace function roznica(a in number, b in number, c in number)
return number is
    wynik number;
begin
    wynik := (a + b) / 2 - c;
    
    return wynik;
end;
```

```sql
set SERVEROUTPUT on;

declare
    wynik number;
begin
    wynik := roznica(3, 5, 2);
    DBMS_OUTPUT.PUT_LINE(wynik);
end;
```

## Zadanie 5

(3) (PL\SQL) Wykorzystaj funkcję z poprzedniego zadania (jeśli nie udało
się go zrobić, to wywołaj funkcję avg), żeby dla każdego pracownika wyświetlić jego imię, nazwisko, oraz różnicę między jego zarobkami, a średnią
zakładanych maksymalnych i minimalnych zarobków dla jego stanowiska
(z tabeli jobs)(2). Wykorzystaj do tego kursor (1).

```sql
set serveroutput on;

declare
    max_salar jobs.max_salary%type;
    min_salar jobs.min_salary%type;
    emp_fname employees.first_name%type;
    emp_lname employees.last_name%type;
    emp_salar employees.salary%type;
    
    cursor c_empl is
        select
            e.first_name,
            e.last_name,
            e.salary,
            j.max_salary,
            j.min_salary
        from employees e
        inner join jobs j
        on e.job_id = j.job_id;
begin
    open c_empl;
    loop
        fetch c_empl into emp_fname, emp_lname, emp_salar, max_salar, min_salar;
            exit when c_empl%notfound;
            dbms_output.put_line(emp_fname || ' ' || emp_lname || ', roznica od sredniej: ' || abs(roznica(min_salar, max_salar, emp_salar)));
    end loop;
    close c_empl;
end;
```

## Zadanie 6

(2) (PL\SQL) Napisz wyzwalacz (1) który będzie zmieniał imię każdego
nowego pracownika na Paweł (1).

```sql
create or replace trigger pan_pawel
before insert on employees
for each row
begin
    :new.first_name := 'Pawel';
end;
```

```sql
insert into employees
    (employee_id, first_name, last_name, email, hire_date, job_id)
values (employees_seq.nextval, 'Jan', 'Kowalski', 'JKOWALSKI', SYSDATE, 'FI_ACCOUNT');
```
