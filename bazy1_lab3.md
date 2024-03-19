# Bazy danych 1 - laboratorium 3

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Wyświetl minimalną i maksymalną kwotę wypłaty dla każdego z działów.
Nadaj kolumnom polskie nazwy. Posortuj wyniki rosnąco zgodnie z różnicą
między tymi dwoma wartościami. Nie wyświetlaj wyników dla działów,
które nie mają ID.

```sql
select 
    department_id as "Id dzialu",
    min(salary) as "Najmniejsza wyplata",
    max(salary) as "Najwieksza wyplata"
from employees
group by department_id 
having department_id is not null
order by "Najwieksza wyplata" - "Najmniejsza wyplata"
```

## Zadanie 2

Dla każdego managera wypisz najdłuższy staż spośród jego pracowników
(na dzień dzisiejszy) w pełnych przepracowanych latach.

```sql
select
    manager_id,
    max(floor(MONTHS_BETWEEN(SYSDATE,hire_date) / 12))
        as "Maksymalny staz pracy" 
from employees
group by manager_id
```

## Zadanie 3

Dla każdego stanowiska wypisz liczbę pracowników na nich zatrudnionych
wraz z wartością średnią, odchyleniem standardowym oraz wariancją zarobków. Wartości zaokrąglij do 2 cyfr po przecinku. Wyświetl stanowiska
z więcej niż jednym pracownikiem. Posortuj wyniki malejąco po średniej
zarobków.

```sql
select
    job_id,
    count(employee_id) as "Liczba pracownikow",
    round(avg(salary), 2) as "Srednia zarobkow",
    round(stddev(salary), 2) as "Odchylenie standardowe",
    round(variance(salary), 2) as "Wariancja"
from employees
group by job_id
having count(employee_id) > 1
order by "Srednia zarobkow" desc
```

## Zadanie 4

Wyświetl ile minęło dni od zatrudnienia pierwszego pracownika do zatrudnienia ostatniego pracownika.

```sql
select
    max(hire_date) - min(hire_date) as "Roznica w zatrudnieniu"
from employees
```

## Zadanie 5

Wyświetl najmniejszą sumę zarobków działów (bez wskazywania który to
dział). Nie bierz pod uwagę działu o id 10,20 i 60.

```sql
select
    min(sum(salary))
from employees
where department_id not in (10, 20, 60)
group by department_id
```
