
# INSERT

## 新規レコード挿入

```sql
insert into depertment (deptno, dname, loc) values (50, 'PROGRAMING', 'ARCHITECTURE')
```

## デフォルト値を挿入

```sql
create table D (id integer default 0)
```

```sql
insert into D (name) values ('Bar')
```