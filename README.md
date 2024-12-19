# Apache Iceberg on AWS

1. Query to create the CSV table using athena
    ```
    CREATE EXTERNAL TABLE
      iceberg_tutorial_db.nyc_taxi_csv
      (
            vendorid bigint,
            tpep_pickup_datetime timestamp,
            tpep_dropoff_datetime timestamp,
            passenger_count double,
            trip_distance double,
            ratecodeid double,
            store_and_fwd_flag string,
            pulocationid bigint,
            dolocationid bigint,
            payment_type bigint,
            fare_amount double,
            extra double,
            mta_tax double,
            tip_amount double,
            tolls_amount double,
            improvement_surcharge double,
            total_amount double,
            congestion_surcharge double,
            airport_fee double
      )
      ROW FORMAT DELIMITED
      FIELDS TERMINATED BY ','
      STORED AS TEXTFILE
      LOCATION 's3://<s3-bucket-name>/csv/';
    ```
2. Query to create Apache Iceberg table
    ```
    CREATE TABLE
      iceberg_tutorial_db.nyc_taxi_iceberg 
      (
            vendorid bigint,
            tpep_pickup_datetime timestamp,
            tpep_dropoff_datetime timestamp,
            passenger_count double,
            trip_distance double,
            ratecodeid double,
            store_and_fwd_flag string,
            pulocationid bigint,
            dolocationid bigint,
            payment_type bigint,
            fare_amount double,
            extra double,
            mta_tax double,
            tip_amount double,
            tolls_amount double,
            improvement_surcharge double,
            total_amount double,
            congestion_surcharge double,
            airport_fee double
      )
      PARTITIONED BY (day(tpep_pickup_datetime))
      LOCATION 's3://<s3-bucket-name>/nyc_taxi_iceberg/'
      TBLPROPERTIES ( 'table_type' ='ICEBERG'  );
    ```
3. Query to create Iceberg table for data manipulation
    ```
    CREATE TABLE
      iceberg_tutorial_db.nyc_taxi_iceberg_data_manipulation 
      (
            vendorid bigint,
            tpep_pickup_datetime timestamp,
            tpep_dropoff_datetime timestamp,
            passenger_count double,
            trip_distance double,
            ratecodeid double,
            store_and_fwd_flag string,
            pulocationid bigint,
            dolocationid bigint,
            payment_type bigint,
            fare_amount double,
            extra double,
            mta_tax double,
            tip_amount double,
            tolls_amount double,
            improvement_surcharge double,
            total_amount double,
            congestion_surcharge double,
            airport_fee double
      )
      PARTITIONED BY (day(tpep_pickup_datetime))
      LOCATION 's3://<s3-bucket-name>/nyc_taxi_iceberg_data_manipulation/'
      TBLPROPERTIES ( 'table_type' ='ICEBERG'  );
    ```
4. Insert from CSV table to Iceberg format
    ```
    INSERT INTO iceberg_tutorial_db.nyc_taxi_iceberg
    SELECT 
    *
    FROM "iceberg_tutorial_db"."nyc_taxi_csv" ;
    ```
5. Run a query to look at the Day partition to see how Iceberg works
    ```
    SELECT * FROM nyc_taxi_iceberg WHERE day(tpep_pickup_datetime) =  5 limit 20;
    ```
6. Insert into another Iceberg table for data manipulation
    ```
    INSERT INTO nyc_taxi_iceberg_data_manipulation
    SELECT * FROM nyc_taxi_iceberg;
    ```
7. Update some data to see the change take place 
    ```
    UPDATE nyc_taxi_iceberg_data_manipulation SET passenger_count = 4.0 WHERE vendorid = 2 AND year(tpep_pickup_datetime) =2022;
    ```
8. Select the updated data
    ```
    SELECT * FROM nyc_taxi_iceberg_data_manipulation WHERE vendorid = 2 and year(tpep_pickup_datetime) =2022 limit 10;
    ```
    
9. Delete from iceberg table
    ```
    DELETE FROM nyc_taxi_iceberg_data_manipulation WHERE year(tpep_pickup_datetime) != 2022; 
    ```



## Useful Links

- [youtube video](https://youtu.be/iGvj1gjbwl0) 

