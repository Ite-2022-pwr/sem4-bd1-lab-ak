# Bazy danych 1 - laboratorium 10

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Stwórz tabelę projects z polem id i dowolnymi innymi dwoma polami (można skorzystać z rozwiązania z listy DDL).

```sql
create table PROJECTS
(
    PROJECT_ID number(6) not null,
    PROJECT_NAME varchar2(69) not null,
    CREATE_DATE date default current_date,
    
    constraint project_id_pk primary key (project_id)
);
```

Utwórz sekwencję project_pk zawierającą liczby parzyste zaczynając od 2.

```sql
create sequence project_pk
start with 2
increment by 2;
```

Dodaj do tabeli projects 4 nowe rekordy korzystając z utworzonej
sekwencji.

```sql
insert into projects
    (project_id, project_name)
values (project_pk.nextval, 'Bober');

insert into projects
    (project_id, project_name)
values (project_pk.nextval, 'Likwidacja Bydgoszczy');

insert into projects
    (project_id, project_name)
values (project_pk.nextval, 'Jan Pawel II');

insert into projects
    (project_id, project_name)
values (project_pk.nextval, 'Piwo');
```

Zmodyfikuj utworzoną sekwencję, tak żeby różnica pomiędzy kolejnymi wpisami wynosiła 5.

```sql
alter sequence project_pk
    increment by 5;
```

Usuń sekwencję project_pk

```sql
drop sequence project_pk;
```

## Zadanie 2

Stwórz sekwencję detonate_seq, która będzie cyklicznie odliczała
w dół od 10 do 0.

```sql
create sequence detonate_seq
start with 10
increment by -1
minvalue 0
maxvalue 10
nocache
cycle;
```

Wyświetl ze zgrozą obecną wartość sekwencji

```sql
set serveroutput on unlimited;

declare
    counter number;
begin
    for i in 0..10
    loop
        select detonate_seq.nextval into counter from dual;
        if counter = 0 then
            dbms_output.put_line('AAAAAA ' || to_char(counter));
        else
            dbms_output.put_line('O Boze ' || to_char(counter));
        end if;
    end loop;
end;
```

## Zadanie 3

Stwórz indeks zawierający imię, nazwisko oraz wynagrodzenie pracowników.

```sql
create index employees_salary_idx
on employees (first_name, last_name, salary);
```

Napisz zapytanie, do którego egzekucji zostanie wykorzystany stworzony indeks

```sql
select * from employees
where first_name like 'S%';
```

Sprawdź czy indeks został zastosowany (skorzystaj z wyrażenia `explain plan for`)

```sql
explain plan
    set statement_id = 'Index testing'
    into plan_table
for
    select * from employees
    where first_name like 'S%';
    
SELECT id, LPAD(' ',2*(LEVEL-1))||operation operation, options,
       object_name, object_alias, position 
    FROM plan_table 
    START WITH id = 0 AND statement_id = 'Index testing'
    CONNECT BY PRIOR id = parent_id AND statement_id = 'Index testing'
    ORDER BY id;
```

## Zadanie 4

Stwórz indeks bazujący na 3 pierwszych cyfrach numeru telefonu pracownika.

```sql
create index empl_phone_idx
on employees (substr(phone_number, 1, 3));
```

Napisz zapytanie, do którego egezukcji zostanie wykorzystany stworzony indeks.

```sql
select * from employees where substr(phone_number, 1, 3) not like '011%';
```