Error
SQL query: Copy


-- a) Delete records that are missing a compilation date
DELETE FROM `unclaimed_assets`
WHERE `compilation_date` IS NULL OR TRIM(`compilation_date`) = '';
MySQL said: Documentation

#1205 - Lock wait timeout exceeded; try restarting transaction
