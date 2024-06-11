# Bazy danych 1 - laboratorium 13

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Napisz procedurę usuwającą wszystkich pracowników na zadanym w argumencie stanowisku. Wyświetl komunikat o tym, ile pracowników zostało
usuniętych (również jeśli nie został usunięty żaden pracownik).

```sql
create or replace procedure delete_employees(job_name in varchar2) is
employees_deleted number;
begin
    delete from employees e
    where e.job_id = job_name;
    employees_deleted := sql%rowcount;
    dbms_output.put_line('Usunieto ' || employees_deleted || ' pracownikow');
end;
```

```sql
set serveroutput on size 3000;

execute delete_employees('&job_name');
```

## Zadanie 2

Stwórz kursor zawierający imiona, nazwiska, adresy email oraz numery
telefonu osób pracujących w zadanym dziale. Wykorzystaj go, żeby wyświetlić wizytówki (w postaci "Imię nazwisko, adres email, nr telefonu")
wszystkich pracowników działu Shipping.

```sql
set serveroutput on;

declare 
    cursor c_employees(p_department_name in varchar2) is
        select
            e.first_name,
            e.last_name,
            e.email,
            e.phone_number
        from employees e
        inner join departments d
        on e.department_id = d.department_id
        where d.department_name = p_department_name;
begin
    for emp in c_employees('Shipping') loop
        DBMS_OUTPUT.PUT(emp.first_name);
        DBMS_OUTPUT.PUT( ' ' || emp.last_name);
        DBMS_OUTPUT.PUT(', ' || emp.email);
        DBMS_OUTPUT.PUT_LINE(', ' || emp.phone_number);
    end loop;
end;
```

## Zadanie 3

Napisz wyzwalacz, który przy dodawaniu nowego pracownika do tabeli
employees ustawi jego adres mailowy na pierwszą literę imienia i całe
nazwisko dużymi literami (np. dla Jana Kowalskiego adres mailowy to
JKOWALSKI). Przetestuj działanie wyzwalacza

```sql
create or replace trigger add_email
before insert or update on employees
for each row
begin
    select upper(substr(:NEW.first_name, 1, 1) || :NEW.last_name) into :NEW.email from dual;
end;
```

```sql
insert into employees(employee_id, first_name, last_name, hire_date, job_id)
values (EMPLOYEES_SEQ.nextval, 'Jan', 'Kowalski', SYSDATE, 'PR_REP');
```

## Zadanie 4

Napisz wyzwalacz, który przy zmianie wartości pola min_salary w tabeli jobs zwiększy wartość wypłaty pracownikom na danym stanowisku w
przypadku gdy ich obecne zarobki są niższe od nowej wartości minimalnej.
Przetestuj działanie wyzwalacza.

```sql
create or replace trigger pay_rise
after update on jobs
for each row
begin
    update employees
    set salary = greatest(:new.min_salary, salary)
    where job_id = :new.job_id;
end;
```

```sql
update jobs
set min_salary = 3000
where job_id = 'ST_CLERK';
```

```sql
select * from employees inner join jobs on employees.job_id = jobs.job_id where employees.job_id = 'ST_CLERK' order by employees.salary;
```
