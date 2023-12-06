
# ORDER BY

## 指定の順序で取得

デフォルト：ASC（省略可）

```sql
select ename,job,sal
		from empty
	where deptno = 10
	order by sal asc
```

```sql
select empno,deptno,sal,ename
		from empty
	where deptno = 10
	order by deptono, sal desc
```
ORDER BY 内での優先順位は、左から右
## 部分文字列でソート

job フィールドを 最後の2文字でソート
```sql
select ename, job
	from empty
order by substr(job, length(job)-1)
```

## 英数字の混合データをソート

※MySQLでは、TRANSLATE関数がサポートされていないのでできない

`TRANSLATE(string, search, translate)` :
- 文字列を文字単位に置換する、改行コード（任意の文字）を削除する
- string : 置換したい対称の文字列全体
- search：置換したい検索文字列
- translate： 置換後の文字列

`REPLACE ( string , search [, replace] )``
- string：置換対象文字列全体
- search：置換したい検索単語
- replace：置換後の単語（default: NULL）

```sql
/* ベースとなるVIEW */
create view V as select ename||' '||deptno as data from empolyee
```
　↓
```
data
----------
SMITH 10
ALLEN 30
WARD 20
```

```sql
/* deptno でソート */
/* 数字を # に変換 → # を削除(英字のみ残る) → data から 英字のみ削除（数字が残る）→ data から数字のみ削除（英字が残る） */
select data from V
	order by replace(data, replace(translate(data, '0123456789', '#########'), '#', ''), '');

/* ename でソート */
/* 数字を # に変換 → # を削除(英字のみ残る) → data から 英字のみ削除（数字が残る） */
select data from V
	order by replace(translate(data, '0123456789', '#########'), '#', '');
```

## NULL を許容する列のソート

```sql
select ename,sal,comm from employee order by 3; /* NULLは下にいく */

select ename,sal,comm from employee order by 3 desc; /* NULLは上にいく */
```

MySQL、PostgreSQL
```sql
/* 昇順 (NULLは下)*/
select ename,sal,comm,
	from (select ename,sal,comm,
		case when comm is NULL then 0 else 1 end as is_null
		from employee ) x
	order by is_null desc, comm;

/* 降順 (NULL は下)*/
select ename,sal,comm,
	from (select ename,sal,comm,
		case when comm is NULL then 0 else 1 end as is_null)
		from employee ) x
	order by is_null desc, comm desc
```

Oracle
```sql
select ename, sal, comm from employee order by comm, nulls last

select ename, sal, comm from employee order by comm, nulls first
```

## データ依存のキーに対してソートする

job が 'SALESMAN' なら、comm でソート、そうでないなら sal でソート のように
値によってソートする列を変えたい場合

```sql
select ename,sal,job,comm
	from employee
order by case when job = 'SALESMAN' then comm else sal end
```

#SQL 