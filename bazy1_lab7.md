# Bazy danych 1 - laboratorium 7

Autor: [Artur Kręgiel](https://github.com/arkregiel)

Prowadząca: [mgr inż. Weronika Węgier](https://www.kssk.pwr.edu.pl/users/wegier)

## Zadanie 1

Utwórz nową tabelę o nazwie projects, o polach project_id, project_name, supervisor_id, create_date, bonus. Pole project_id
ma być kluczem głównym, a pole supervisor_id ma być kluczem obcym
wskazującym na pracownika z tabeli employees. Pola z id oraz nazwą
projektu nie mogą być puste. W polu create_date ustaw wartość domyślną będącą datą wprowadzenia rekordu. Sprawdź czy kwota wpisywana w pole bonus jest większa od 0. Dodaj tabelę która umożliwiałaby
przypisywanie pracowników do projektów przyjmując, że jeden pracownik
może uczestniczyć w wielu projektach

```sql
create sequence project_seq start with 1;
```

```sql
create table PROJECTS
(
    PROJECT_ID number(6) default project_seq.nextval not null,
    PROJECT_NAME varchar2(20) not null,
    SUPERVISOR_ID number(6) not null,
    CREATE_DATE date default current_date,
    BONUS number(8,2),
    
    constraint project_id_pk primary key (project_id),
    
    constraint supervisor_id_fk 
    foreign key (supervisor_id)
    references employees(employee_id),
    
    constraint bonus_positive check (bonus > 0)
);
```

```sql
create table assigned_projects
(
    id number(6) not null,
    project_id number(6) not null,
    employee_id number(6) not null,
    
    constraint id_pk primary key (id),
    
    constraint project_id_fk 
    foreign key (project_id)
    references projects(project_id),
    
    constraint employee_id_fk 
    foreign key (employee_id)
    references employees(employee_id)
);
```

## Zadanie 2

Dodaj do utworzonej tabeli kolumnę project_email. Wartości w kolumnie muszą być unikatowe.

```sql
alter table projects
    add PROJECT_EMAIL varchar2(25) unique;
```

## Zadanie 3

Stwórz tabelę, w której znajdą się wszyscy managerowie. Jej struktura ma
być analogiczna do struktury tabeli employees.

```sql
create table MANAGERS as
(
select *
    from employees
    where 
    employee_id
    in 
    (select distinct manager_id from employees)
);
```

```sql
alter table managers
    add constraint manager_id_pk 
    primary key (employee_id);
```

## Zadanie 4

Zmodyfikuj tabelę employees w ten sposób, żeby pole manager_id
wskazywało na odpowiednie osoby z tabeli z managerami.

```sql
alter table employees
    add constraint manager_id_fk 
    foreign key (manager_id)
    references managers(employee_id);
```

## Zadanie 5

Usuń wszystkie utworzone tabele.

```sql
drop table managers cascade constraints;
drop table assigned_projects cascade constraints;
drop table projects cascade constraints;
```
