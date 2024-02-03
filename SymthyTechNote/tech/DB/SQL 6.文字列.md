
## 文字列反復処理

```sql
select substr(e.ename, iter.pos, 1) as C
	from (select ename from emp where ename = 'KING') e,
		 (select id as pos from t10) iter
	where iter.pos <= length(e.ename)
```

結果:
```sql
C
-
K
I
N
G
```

### 過程
```sql
select ename, iter.pos 
	from (select ename from emp where ename = 'KING') e,
		(select id as pos from t10) iter
```
結果
```sql
ENAME          POS
-------- ---------
KING             1
KING             2
KING             3
KING             4
KING             5
KING             6
KING             7
KING             8
KING             9
KING            10
```

```sql
select ename, iter.pos
	from (select ename from emp where ename = 'KING') e,
		(select id as pos from t10) iter
	where iter.pos <= length(e.ename)
```
結果
```sql
ENAME          POS
-------- ---------
KING             1
KING             2
KING             3
KING             4
```

## 2. 文字列リテラル内にクォートを埋め込む

```sql
'''' → '
```

## 3. 文字列内の文字の出現回数をカウント

length と replace の組み合わせ
```sql
select 
	(length('HELLO HELLO') - length(replace('HELLO HELLO', 'LL', ''))) / length('LL') as correct_cnt,
	(length('HELLO HELLO') - length(replace('HELLO HELLO', 'LL', ''))) as incorrect_cnt
from t1 
```
結果
```sql
CORRECT_CNT INCORRECT_CNT
----------- -------------
          2             4
```

## 4. 文字列から不要な文字を取り除く
replace と translate

oracle, postgresql, sqlserver
```sql
select ename, replace(translate(ename, 'aaaaa', 'AEIOU'), 'a', '') as stripped1,
	sal, replace(cast(sal as char(4)), '0', '') as stripped2
from emp
```
結果
```sql
ENAME    STRIPPED1      SAL STRIPPED2
-------- --------- -------- ---------
SMITH    SMTH           800 8
```


## 5. 数値データと文字データを分離

```sql

```