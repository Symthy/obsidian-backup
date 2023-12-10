
# INSERT

## 新規レコード挿入

```sql
insert into depertment (deptno, dname, loc) values (50, 'PROGRAMING', 'ARCHITECTURE')
```

## デフォルト値を挿入

```sql
create table D (id integer default 0)
insert into D values (default) /* 全列default値の行追加 */]
insert into D (id) values (default) /* idのみdefault値の行追加 */
```

```sql
create table D (id integer default 0, name varchar(10))
insert into D (name) values ('Bar') /* id：0、name：'Bar'　の行追加 */
```

## デフォルト値をNULLにオーバーライドする

```sql
create table D (id integer default 0, name VARCHAR(10))

insert into d (id, name) value (null, 'Brighten') /* これ。指定なしはデフォルト値になる */
```

## 別のテーブルの行をコピー

```sql
insert into dept_east(deptno, dname, location)
	select deptno, dname, location from depertmentt where location in ('NEW YORK', 'BOSTON')
```

## テーブル定義をコピー

Oracle、MySQL、Postgresql
行を返さないサブクエリで create table

```sql
create table depertment2 as select * from depertment where 1 = 0
```

create table as select (CTAS) は、where 句に偽の条件を指定しない限り、作成するテーブルにクエリからのすべての行を設定。

## 一度に複数テーブルに挿入

あるクエリが返す行を、複数のテーブルに挿入

Oracle：insert all か insert first を使う
```sql
insert all 
	when location in ('NEW YORK', 'BOSTON') then
		into dept_east (deptno, dname, location) values (deptno, dname, location)
	when location = 'CHICAGO' then 
		into dept_mid (deptno, dname, location) values (deptno, dname, location)
	else 
		into dept_west (deptno, dname, location) values (deptno, dname, location)
	select deptno, dname, location from depertment
```

MySQL、Postgresql：未サポート？
