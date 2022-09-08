### Example
```sql  
CREATE OR REPLACE FUNCTION  
    partition_for_cars()  
    RETURNS TRIGGER AS  
$$  
BEGIN  
    IF (NEW.id >= 0 and NEW.id < 1000) THEN  
        INSERT INTO cars_1 VALUES (new.id, new.brand, new.model,  
                                   new.owner_id, new.created_by,  
                                   new.created_date, new.updated_by,  
                                   new.modified_date);  
    ELSIF (NEW.id >= 100 AND  
           NEW.id < 2000) THEN  
        INSERT INTO cars_2 VALUES (new.id, new.brand, new.model,  
                                   new.owner_id, new.created_by,  
                                   new.created_date, new.updated_by,  
                                   new.modified_date);  
    ELSE        RAISE EXCEPTION 'Id out of range.  
        Fix the car_insert_trigger() function!';  
    END IF;    RETURN NULL;END;  
$$  
    LANGUAGE plpgsql;  
  
create trigger partition_cars before insert on car.car for each row execute procedure partition_for_cars();
------
create table cars_3 (like car.car including all);  
alter table cars_3 inherit car.car;
```