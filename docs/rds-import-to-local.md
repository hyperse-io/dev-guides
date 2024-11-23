# Export mysql data from `rds` to local mysql

1. The local database retains the database name, deletes all tables, executes schema sync locally to generate data tables and structures, and clears the default data of all data tables.
2. Run the resulting statement executed by 'sql' below, remove all the 'table' foreign key dependencies.

```sql
SELECT CONCAT('ALTER TABLE ','`',TABLE_SCHEMA,'`','.`',TABLE_NAME,'` DROP FOREIGN KEY ',CONSTRAINT_NAME,' ;')
	FROM information_schema.TABLE_CONSTRAINTS c
		WHERE c.TABLE_SCHEMA='hyperse-main' AND c.CONSTRAINT_TYPE='FOREIGN KEY';
```

3. RDS exports all data tables (data, excludes db structures) of the entire database. Execute the 'db_dump.sql' exported by RDS.
4. mysql terminal (`mysql -uroot -p`), run `source db_dump.sql`
5. cd local project `dev-server`, run `yarn serve` to sync structure to recovery foreign key dependencies.
