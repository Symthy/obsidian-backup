
# SELECT

## WHERE

### 複数条件を満たす行取得

```sql
select from emp where (deptno = 10 or comm is not null or sal <= 200) and deptno = 20;
```

### エイリアス化された列を参照

```sql
select * from (select sal as salary, comm as commission from emp) x where salary < 500;
```

### 列値の連結

Oracle DB, PostgreSQL
```sql
select ename||' WORKS AS A '||job as msg from emp where deptno=10
```

MySQL
```sql
select concat(ename, ' WORKS AS A ', job) as msg
```

```
ENAME      JOB       MSG
---------- --------- ----------
CLARK      MANAGER   CLARK WORKS AS A MANAGER
KING       PRESIDENT KING WORKS AS A PRESIDENT
MILLER     CLERK     MILLER WORKS AS A CLERK
```

## CASE式

case 式は、ラベルの読み替えしかしない。レコードの集約はしない

### SELECT 文で条件ロジックを使う

```sql
select ename, sal,
		case when sal <= 200 then 'UNDERPAID'
		     when sal >= 400 then 'OVERPAID'
		     else 'OK'
		end as status
	from emp
```

### 異なる条件の集計を行う

```sql
select perf_name,
		SUM( case when sex = '1' then population else 0 end ) as count_man,
		SUM( case when sex = '2' then population else 0 end ) as count_feman
	from pop_tbl
	group by perf_name;
```

SUM だけでなく、COUNT や AVG でも使える

## LIKE

### パターン検索（ワイルドカード）

%： ワイルドカード

```sql
select ename, job
	from emp
where deptno in (10, 20)
	and (ename like '%I%' or job like '%ER');
```

## NULL 

### NULL を探す

```sql
select * from emp where comm is null;
```

### NULL を実際の値に変換 coaleace

```sql
select coaleace(com, 0)
```


#SQL 
