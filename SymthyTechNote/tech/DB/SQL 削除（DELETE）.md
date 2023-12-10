
# DELETE

## テーブルから全レコード削除

```sql
delete from employee
```

## 特定のレコードを削除

```sql
delete from employee where deptno <= 10
```

## １つのレコードを削除

```sql
delete from employee where empno = 7782
```

## 参照相互違反を削除

```sql
delete from employee where not exists (select * from depertment where depertment.deptno = emp.deptno) 
```

```sql
delete from employee where deptno not in (select deptno from depertment)
```

## 重複レコードを削除

```sql
delete from dupes where id not in 
	(select min(id) from (select id, name from dupes) tmp group by name)
```

※重複レコードの定義が大事
同じnameでグルーピングしたら、重複してたら重複データの id が select で欠ける ので、それを削除している

## 他のテーブルから参照されているレコードを削除


```sql
delete from employee where deptno in 
	(select deptno from dept_accidents group by deptno having count(*) >= 3)
```
