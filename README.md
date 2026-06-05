--  5. One-Time Data Cleaning Migration
--     Run this once on existing databases to:
--       a) Delete rows that have no compilation date
--       b) Strip timestamps from date_of_birth and compilation_date
--       c) Convert both columns to proper DATE type
-- ============================================================

-- a) Delete records that are missing a compilation date
DELETE FROM `unclaimed_assets`
WHERE `compilation_date` IS NULL OR TRIM(`compilation_date`) = '';

-- b) Clean up date_of_birth: strip timestamps and standardize to YYYY-MM-DD
UPDATE `unclaimed_assets`
SET `date_of_birth` = DATE_FORMAT(
    COALESCE(
        STR_TO_DATE(`date_of_birth`, '%Y-%m-%d %H:%i:%s'),
        STR_TO_DATE(`date_of_birth`, '%Y-%m-%d'),
        STR_TO_DATE(`date_of_birth`, '%d/%m/%Y %H:%i:%s'),
        STR_TO_DATE(`date_of_birth`, '%d/%m/%Y'),
        STR_TO_DATE(`date_of_birth`, '%d-%m-%Y %H:%i:%s'),
        STR_TO_DATE(`date_of_birth`, '%d-%m-%Y')
    ),
    '%Y-%m-%d'
)
WHERE `date_of_birth` IS NOT NULL AND TRIM(`date_of_birth`) <> '';

-- c) Clean up compilation_date: strip timestamps and standardize to YYYY-MM-DD
UPDATE `unclaimed_assets`
SET `compilation_date` = DATE_FORMAT(
    COALESCE(
        STR_TO_DATE(`compilation_date`, '%Y-%m-%d %H:%i:%s'),
        STR_TO_DATE(`compilation_date`, '%Y-%m-%d'),
        STR_TO_DATE(`compilation_date`, '%d/%m/%Y %H:%i:%s'),
        STR_TO_DATE(`compilation_date`, '%d/%m/%Y'),
        STR_TO_DATE(`compilation_date`, '%d-%m-%Y %H:%i:%s'),
        STR_TO_DATE(`compilation_date`, '%d-%m-%Y'),
        STR_TO_DATE(`compilation_date`, '%d-%b-%Y'),
        STR_TO_DATE(`compilation_date`, '%d-%M-%Y'),
        STR_TO_DATE(`compilation_date`, '%d/%b/%Y'),
        STR_TO_DATE(`compilation_date`, '%d/%M/%Y')
    ),
    '%Y-%m-%d'
)
WHERE `compilation_date` IS NOT NULL AND TRIM(`compilation_date`) <> '';

-- d) Alter column types to DATE (removes timestamp storage permanently)
ALTER TABLE `unclaimed_assets` MODIFY COLUMN `date_of_birth` DATE NULL;
ALTER TABLE `unclaimed_assets` MODIFY COLUMN `compilation_date` DATE NULL;
