
# UPDATE

UPDATE (表名) SET (カラム名1) = (値1) WHERE (条件);

## テーブル内のレコードを変更

```sql
update employee set sal = sal*1.1 where deptno = 20
```

## 対応する行が存在するなら更新

```sql
update employee set sal = sal*1.1 where empno (select empno from emp_bonus)
```

```sql
update employee set sal = sal*1.1 where exits 
	(select null from emp_bonus where emp.empno = emp_bonus.empno)
```
## 別のテーブルの値で更新する

MySQL 
```sql
update employee e, new_sal ns set e.sal = ns.sal, e.comm = ns.sal/2
	where e.deptno = ns.deptno 
```

Oracle
```sql
update 
	(select e.sal as emp_sal, e.comm as emp_comm, ns.sal as ns_sal ns.sal/2 as ns_comm
		from employee e, new_sal ns where e.deptno = ns.deptno) 
	set emp_sal = ns_sal, emp_comm = ns_comm
```

Postgresql
```sql
update employee set sal = ns.sal, comm = ns.sal/2
	from new_sal ns where e.deptno = ns.deptno 
```

## レコードをマージ
レコードが存在すれば更新、存在しなければ挿入、行の更新後にある条件を満たさなければ削除

```sql
merge into emp_commission ec 
	using (select * from employee) emp on (ec.empno = emp.empno)
	when matched then 
		update set ec.comm = 1000
		delete where (sal < 200)
	when not matched then
		insert (ec.empno, ec.ename, ec.deptno, ec.comm)
		values (emp.empno, emp.ename, emp.deptno, emp.comm)
```