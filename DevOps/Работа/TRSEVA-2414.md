
Скрипт SQL для создания публикаций на основе схем:

```sql
DO $$
DECLARE
    first_schema TEXT;
    schema_name TEXT;
BEGIN
    -- Get the first schema name for the CREATE statement
    SELECT nspname INTO first_schema
    FROM pg_namespace
    WHERE nspname LIKE 'Trs.%'
    AND nspname NOT LIKE 'Trs.Configuration'
    AND nspname NOT LIKE 'Trs.Scheduler'
    AND nspname NOT LIKE 'Trs.Semaphore'
    AND nspname NOT LIKE 'Trs.CiEvent'
    LIMIT 1;

    -- Create the publication for the first schema
    IF first_schema IS NOT NULL THEN
        EXECUTE 'CREATE PUBLICATION pubdict207 FOR TABLES IN SCHEMA "' || first_schema || '";';
    END IF;

    -- Loop through remaining schemas and add to the publication
    FOR schema_name IN 
        SELECT nspname
        FROM pg_namespace
        WHERE nspname LIKE 'Trs.%'
        AND nspname NOT LIKE 'Trs.Configuration'
        AND nspname NOT LIKE 'Trs.Scheduler'
        AND nspname NOT LIKE 'Trs.Semaphore'
        AND nspname NOT LIKE 'Trs.CiEvent'
        AND nspname <> first_schema
    LOOP
        EXECUTE 'ALTER PUBLICATION pubdict207 ADD TABLES IN SCHEMA "' || schema_name || '";';
    END LOOP;
END $$;
```

Настройка репликации:
