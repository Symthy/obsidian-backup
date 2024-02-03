

## 1. テーブル一覧表示

oracle
```sql
select table_name from all_tables where owner = 'SMAGOL'
```

postgresql, mysql, sql server
```sql
select table_name from information_schema.tables where table_schema = 'SMAGOL'
```

## 2. テーブルの列一覧表示

oracle
```sql
select column_name, data_type, column_id from all_tab_columns 
    where owner = 'SMEAGOL' and table_name = 'EMP'
```

postgresql, mysql, sqlserver
```sql
select column_name, data_type, ordinal_position from infomation_schema.columns
    where table_schema = 'SMEAGOL' and table_name = 'EMP'
```

## 3. テーブルのインデックス付けされた列の一覧表示

oracle
```sql
select table_name, inde_name, column_name, column_position
    from sys.all_ind_columns
	    where table_name = 'EMP' and table_owner = 'SMEAGOL'
```

postgresql
```sql
select a.tablename, a.indexname, b.column_name
	from pg_catalog.pg.indexes a, information_schema.columns b
		where a.schemaname = 'SMEAGOL' and a.tablename = b.table_name
```

mysql
```sql
show index from table_name
```

## 4. テーブルに対する制約の一覧表示

oracle
```sql
select a.table_name, a.constraint_name, b.column_name, a.constrainrt_type
	from all_constraints a, all_cons_columns b
		where a.table_name = 'EMP' 
			and a.owner = 'SMEAGOL' 
			and a.table_name = b.table_name
			and a.owner = b.owner
			and a.constraint_name = b.constraint_name
```

postgresql, mysql, sqlserver
```sql
select a.table_name, a.constraint_name, b.column_name, a.constraint_type
	from information_schema.table_constraints a,
		information_schema.key_column_usage b,
		where a.table_name = 'EMP' 
			and a.table_schema = 'SMEAGOL' 
			and a.table_name = b.table_name
			and a.table_schema = b.table_schema
			and a.constraint_name = b.constraint_name
```

## 5. 対応するインデックスがない外部キー一覧表示

oracle
```sql
select a.table_name, a.constraint_name, a.column_name, c.index_name
	from all_cons_columns a, all_constraints b, all_ind_columns c
		where a.table_name = 'EMP'
			and a.owner = 'SMEAGOL'
			and b.constraint_type = 'R'
			and a.owner = b.owner
			and a.table_name = b.table_name
			and a.owner = c.table_owner (+)
			and a.table_name = c.table_name (+)
			and a.column_name = c.column_name (+)
			and c.index_name is null
```

postgresql
```sql
select fkeys.table_name, fkeys.constraint_name, fkeys.column_name, ind_cols.indexname
	from (
		select a.constraint_schema, a.table_name, a.constraint_name, a.column_name
			from information_schema.key_column_usage a,
				information_schema.referential_constraints b
			where a.constraint_name = b.constraint_name
				and a.constraint_schema = b.constraint_schema
				and a.constraint_schema = 'SMEAGOL'
				and a.table_name = 'EMP'
		) fkeys
	left join (
		select a.schemaname, a.tablename, a.indexname, b.column_name
			from pg_catalog.pg_indexes a, information_schema.columns b
			where a.table_name = b.table_name
				and a.schemaname = b.table_schema
		) ind_cols
	on (fkyes.constraint_schema = ind_cols.schemaname
		and fkeys.table_name = ind_cols.tablename
		and fkeys.column_name = ind_cols.column_name)
	where ind_cols.indexname is null
```

mysql
```sql
show index
```
その結果を、INFORMATION_SCHEMA.KEY_COLUMN_USAGE.COLUMN_NAME の結果と比較
COLUMN_NAME が KEY_SOLUMN_USAGE に表示されても show index では返されなければ、その列はインデックス付けされていないことになる

## 6. SQLを使ってSQLを生成

全てのテーブルのすべての行数をカウントするSQLを生成
```sql
select 'select count(*) from '||table_name||';' cnts from user_tables;
```

すべてのテーブルから外部キーを無効
```sql
select 'alter table '||table_name||' disable constraint '||constraint_name||';' cons
	from user_constraints where constraint_type = 'R';
```

テーブルの列から挿入スクリプトを生成
```sql
select 'insert into emp(empno,ename,hiredate) '||chr(10)||
	'value( '||empno||','||''''||ename||''',todate('||''''||hiredate||''') );' inserts
	from emp where depert_no = 10;
```

## 7. Oracle データディクショナリー表示

ディクショナリービュー と その目的表示
```sql
select table_name, comments from directionary order by table_name;
```

DICT_COLUMNSに問い合わせ、指定したディクショナリービューの列表示
```sql
select column_name, comments from dict_columns
	where table_name = 'ALL_TAB_COLUMNS';
```

スキーマ内のテーブルを表すビューを表示
```sql
select table_name, comments from dictionary
	where table_name LIKE '%TABLE%'
		order by table_name;
```