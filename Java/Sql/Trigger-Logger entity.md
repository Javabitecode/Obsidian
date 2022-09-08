# Example
### Логирование сущности по методам sql
```sql
CREATE TYPE dml_type AS ENUM ('INSERT', 'UPDATE', 'DELETE');

  

CREATE TABLE IF NOT EXISTS car_audit_log

(

    car_id        bigint    NOT NULL,

    old_row_data  jsonb,

    new_row_data  jsonb,

    dml_type      dml_type  NOT NULL,

    dml_timestamp timestamp NOT NULL,

    PRIMARY KEY (car_id, dml_type, dml_timestamp)

);

  

CREATE OR REPLACE FUNCTION car_audit_trigger_func()

    RETURNS trigger AS

$body$

BEGIN

    if (TG_OP = 'INSERT') then

        INSERT INTO car_audit_log (car_id,

                                   old_row_data,

                                   new_row_data,

                                   dml_type,

                                   dml_timestamp)

        VALUES (NEW.id,

                null,

                to_jsonb(NEW),

                'INSERT',

                CURRENT_TIMESTAMP);

        RETURN NEW;

    elsif (TG_OP = 'UPDATE') then

        INSERT INTO car_audit_log (car_id,

                                   old_row_data,

                                   new_row_data,

                                   dml_type,

                                   dml_timestamp)

        VALUES (NEW.id,

                to_jsonb(OLD),

                to_jsonb(NEW),

                'UPDATE',

                CURRENT_TIMESTAMP);

        RETURN NEW;

    elsif (TG_OP = 'DELETE') then

        INSERT INTO car_audit_log (car_id,

                                   old_row_data,

                                   new_row_data,

                                   dml_type,

                                   dml_timestamp)

        VALUES (OLD.id,

                to_jsonb(OLD),

                null,

                'DELETE',

                CURRENT_TIMESTAMP);

        RETURN OLD;

    end if;

END;

$body$

    LANGUAGE plpgsql;

  

CREATE TRIGGER car_audit_trigger

    AFTER INSERT OR UPDATE OR DELETE

    ON car

    FOR EACH ROW

EXECUTE FUNCTION car_audit_trigger_func()
```