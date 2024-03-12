# Bazy danych 1 - laboratorium 2

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Wyświetl strukturę tabeli employees.

```sql
select * from employees;
select * from all_tables where table_name = 'EMPLOYEES';
describe employees
```

## Zadanie 2

Wyświetl imiona, nazwiska oraz maile wszystkich pracowników.

```sql
select first_name, last_name, email from employees
```

## Zadanie 3

Wyświetl nazwy wszystkich stanowisk na jakich zatrudnieni są pracownicy.
Stanowiska mają się nie powtarzać.

```sql
select distinct job_id from employees order by 1
```

## Zadanie 4

Wyświetl imiona i nazwiska wszystkich pracowników zatrudnionych na
stanowisku st_clerk

```sql
select first_name, last_name from employees where job_id = 'ST_CLERK'
```

## Zadanie 5

 Wyświetl nazwiska, imiona, numer telefonu i adres email wszystkich pracowników zatrudnionych w departamencie o id 50. Niech lista będzie uporządkowana alfabetycznie po nazwiskach.

```sql
select last_name, first_name, phone_number, email from employees where department_id = 50 order by 1
```

## Zadanie 6

Wyświetl nazwiska, imiona oraz numer telefonu wszystkich pracowników
przypisanych do managera o id 100. Przetłumacz nazwy kolumn na język
polski.

```sql
select last_name as nazwisko, first_name as imie, phone_number as "numer telefonu" from employees where manager_id = 100; -- 6
```

## Zadanie 7

Wyświetl imiona, nazwiska, stanowiska oraz wypłaty wszystkich osób zatrudnionych na stanowisku sa_rep których wypłata jest niewiększa niż
10000.

```sql
select first_name, last_name, salary from employees where job_id = 'SA_REP' and salary <= 10000; -- 7
```

## Zadanie 8

Wyświetl wszystkich pracowników, których wypłata jest mniejsza niż 2500
lub większa niż 12000. Posortuj wyniki malejąco po wypłacie.

```sql
select * from employees where salary < 2500 or salary > 12000 order by salary desc; -- 8
```

## Zadanie 9

Zrób poprzednie zadanie na 2 różne sposoby (z dwoma różnymi warunkami).

```sql
select * from employees where salary < 2500 union select * from employees where salary > 12000 order by 8 desc; -- 9
select * from employees where salary not between 2500 and 12000 order by salary desc; -- 9
```
