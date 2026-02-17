
## PostgreSQLテーブル オブジェクトID(relid)を調べる
````sql
psql DBNAME -c "
select relid, relname from pg_stat_all_tables 
where relname = 'TABLENAME";
"
````

## 物理ファイル場所

$PGDATA/base/OID/ ...ファイル OID_fsm , OID_vm 

## ObjectIDの型 4bytes (32bit) 

````sql
select oid,relname from pg_class;
````
