# Bazy danych 1 - laboratorium 12

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Napisz program drukujący napis "Hello world!"

```sql
set serveroutput on size 30000;
begin
dbms_output.put_line('Hello, World!');
end;
```

## Zadanie 2

Wyświetl średnią geometryczną maksymalnych i minimalnych zarobków
w firmie. Skorzystaj z zapytania z select into.

```sql
set serveroutput on size 30000;

declare
    avgSalary number;
begin
    select sqrt(max(salary) * min(salary)) into avgSalary
    from employees;
    
    dbms_output.put_line('Średnia geometryczna maksymalnych i minimalnych zarobów w firmie: ' || avgSalary);
end;
```

## Zadanie 3

Napisz funkcję wyliczającą średnią geometryczną dwóch liczb. Wykorzystaj ją w poprzednim zadaniu.

```sql
create or replace function geometric_mean(a in number, b in number)
return number is
    mean number;
begin
    mean := sqrt(a * b);
    return mean;
end;
```

```sql
set serveroutput on size 30000;

declare
    maxSalary number;
    minSalary number;
    avgSalary number;
begin
    select max(salary), min(salary) into maxSalary, minSalary
    from employees;
    
    avgsalary := geometric_mean(maxsalary, minsalary);
    
    dbms_output.put_line('Średnia geometryczna maksymalnych i minimalnych zarobów w firmie: ' || avgSalary);
end;
```

## Zadanie 4

Wyświetl zarobki podanego (imię oraz nazwisko) przez użytkownika pracownika. W przypadku jeśli pracownik nie zostanie znaleziony w bazie, to
wyświetl o tym informację.

```sql
set serveroutput on size 30000;

declare
    emplSalary number;
begin
    select salary into emplSalary
    from employees
    where first_name = '&first_name'
        and last_name = '&last_name';
        
    dbms_output.put_line(emplSalary);
    
    exception
        when no_data_found then
            dbms_output.put_line('No such employee');
        when others then
            dbms_output.put_line('Error');
end;
```

## Zadanie 5

Napisz procedurę, która dla zadanej liczby (a) będzie wypisywała wszystkie liczby aż do jej dwukrotności(2*a), wraz z informacją, czy liczba jest
większa, mniejsza, czy równa a.

```sql
create or replace procedure print_numbers(a in number) as
begin
    
    for i in 0..2*a loop
        if i < a then
            dbms_output.put_line(i || ' jest mniejsze od ' || a);
        elsif i > a then
            dbms_output.put_line(i || ' jest większe od ' || a);
        else
            dbms_output.put_line(i || ' jest równe ' || a);
        end if;
    end loop;
    
end print_numbers;
```

```sql
set serveroutput on size 30000;

execute print_numbers(&a);
```
