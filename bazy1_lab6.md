# Bazy danych 1 - laboratorium 5

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Dodaj nowe stanowisko i zatrudnij na nim 5 nowych pracowników. Niech
pracownicy zarabiają kwotę w połowie między minimalnym a maksymalnym możliwym wynagrodzeniem na tym stanowisku. Postaraj się, żeby
jak najmniej wartości było wpisywanych ręcznie.

```sql
insert into jobs (job_id, job_title, min_salary, max_salary)
values ('ZM_ULC', 'Zamiatacz ulic', 420, 2137);
select * from jobs where job_id = 'ZM_ULC';

insert all
    into employees (employee_id, first_name, last_name, email, job_id, salary, hire_date)
    values
        (207, 'Czarek', 'Czarkowski0', 'czar0@example.com', 'ZM_ULC', (select floor((min_salary + max_salary) / 2) from jobs where job_id = 'ZM_ULC'), current_date)
        
    into employees (employee_id, first_name, last_name, email, job_id, salary, hire_date)
    values
        (208, 'Czarek', 'Czarkowski1', 'czar1@example.com', 'ZM_ULC', (select floor((min_salary + max_salary) / 2) from jobs where job_id = 'ZM_ULC'), current_date)
        
    into employees (employee_id, first_name, last_name, email, job_id, salary, hire_date)
    values
        (209, 'Czarek', 'Czarkowski2', 'czar2@example.com', 'ZM_ULC', (select floor((min_salary + max_salary) / 2) from jobs where job_id = 'ZM_ULC'), current_date)
        
    into employees (employee_id, first_name, last_name, email, job_id, salary, hire_date)
    values
        (210, 'Czarek', 'Czarkowski13', 'czar3@example.com', 'ZM_ULC', (select floor((min_salary + max_salary) / 2) from jobs where job_id = 'ZM_ULC'), current_date)
        
    into employees (employee_id, first_name, last_name, email, job_id, salary, hire_date)
    values
        (211, 'Czarek', 'Czarkowski4', 'czar4@example.com', 'ZM_ULC', (select floor((min_salary + max_salary) / 2) from jobs where job_id = 'ZM_ULC'), current_date)
select * from dual;

select * from employees where job_id = 'ZM_ULC'; 
```

## Zadanie 2

Usuń dodane stanowisko.

```sql
delete from employees where job_id = 'ZM_ULC';

select * from employees where job_id = 'ZM_ULC';

delete from jobs where job_id = 'ZM_ULC';

select * from jobs where job_id = 'ZM_ULC';
```

## Zadanie 3

Przepisz pracowników mających managera o najmniejszym wynagrodzenia
na managera o największym wynagrodzeniu, pomijając prezesa firmy

```sql
update employees
set
manager_id=(select employee_id
    from employees
    where 
    employee_id
    in 
    (select distinct manager_id from employees)
	and job_id != 'AD_PRES'
    order by salary desc
    fetch first 1 rows only)
where
manager_id in (select employee_id
    from employees
    where 
    employee_id
    in 
    (select distinct manager_id from employees) 
    order by salary
    fetch first 1 rows only)
;
select * from employees where manager_id = 124;
```

## Zadanie 4

Zmniejsz wynagrodzenie o 15% managerom, którzy zarabiają ponad dwukrotnie więcej niż średnia wartość zarobków ich pracowników.

```sql
update employees
set salary = salary * 0.85
where employee_id in
(select employee_id 
from (select employee_id, salary
    from employees
    where 
    employee_id
    in 
    (select distinct manager_id from employees)
) man1
inner join (select manager_id, avg(salary) as emp_sal from employees group by manager_id) man2
on man1.employee_id = man2.manager_id
where man1.salary > 2 * man2.emp_sal)
```

## Zadanie 5

Oficjalnie zmień stanowisko odpowiednim pracownikom na stanowisko "Shipping Clerk".

```sql

```

## Zadanie 6

Cofnij wszystkie wprowadzone zmiany.
