
# UNION

## 複数のテーブルを統合 (UNION ALL)

- 重複しているものも含める
- 型はあっていないといけない

```sql
select ename as ename_and_dname, deptno from employee
	where deptno = 10
	union all
select '-------', null from t1
	union all
select dname, deptno from dept
```

```
ENAME_AND_DNAME deptno
--------------- ------
EEE                 10
------
DDD                 30
```

## 複数のテーブル統合(重複排除) (UNION)

```sql
select deptno from employee
	union
select deptno from depertment
```

UNION は UNION ALL からの出力に DISTINCT を適用するクエリとほぼ同等
```sql
select distinct deptno 
	from (select deptno from employee union all select deptno from dept) 
```

# 結合

内部結合： 結合すべき行が見つからなかった場合にその行は消滅する。
外部結合： 結合すべき行が見つからなくても諦めず、全ての値が`NULL`である行を生成して結合する

## 関連する行を組み合わせる (等価結合)

- 内部結合の一種

```sql
select e.ename, d.loc from employee e, depertment d
	where e.depertno = d.deptno and e.deptno = 10
```

e.depertno と d.deptno が同じものだけを返す

JOIN の場合（INNERは省略可）

```sql
select e.ename, d.loc from employee e inner join depertment d 
	on (e.deptno = d.deptno)
	where e.deptno = 10
```

## ２つのテーブルに共通する行を取得（集合演算）

```sql
create view V as select ename, job,sal from employee where job = 'CLEAK';
```

MySQL, Oracle、PostgreSQL 共通
```sql
select e.empno, e.ename, e.job, e.deptno from employee e, V
	where e.ename = V.ename and e.job = V.job
```

```sql
select e.empno, e.ename, e.job, e.deptno from employee e join V
	on (e.ename = v.ename and e.job = V.job)
```

Oracle、PostgreSQL：ビューからの値を返す必要がある場合

```sql
select empno, ename, job, deptno from employee where (ename,job) 
	in (select ename, job from employee intersect select ename, job from V)
```

## テーブルAからテーブルBには存在しない値を取得

PostgreSQL
```sql
select deptno from depertment
	except select deptno from employee
```

Oracle
```sql
select deptno from depertment
	minus select deptno from employee
```

MySQL
```sql
select deptno from depertment
	where deptno not in (select deptno from employee)
```

## テーブルAからテーブルBにない行を取得する(反結合)

```sql
select d.* from depertment d left outer join employee 
	on (d.deptno = e.deptno) where e.deptno is null
```

## 他の結合を妨げずにクエリに結合を追加する

```sql
select e.ename, d.loc, eb.received from employee e join depertment d 
	on (e.deptno = d.deptno) left join emp_bonus eb on (e.empno = eb.empno)
```

外部結合を使うと、元のクエリからの情報を失うことなく追加情報を得られる

## ２つのテーブルが同じデータを持つか判定


## 複数テーブルから欠損データを取得


## (NULLを許容する列で) 演算や比較でNULLを使う

NULLを標準的な評価に使える実際の値に変換

```sql
select ename, comm from employee
	where coalesce(comm, 0) < (select comm from employee where ename = 'WARD')
```

