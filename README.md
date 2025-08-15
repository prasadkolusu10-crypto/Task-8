# Task 8: Stored Procedures and Functions in MySQL
## Objective
Learn how to modularize and reuse SQL logic using **stored procedures** and **functions**.
---
### 1. Create the Database and Use It
```MySQL
create database Store;
Query OK, 1 row affected (0.03 sec)
 use Store;
Database changed
create table customers (customer_id int auto_increment primary key,name varchar(50),email varchar(50));
Query OK, 0 rows affected (0.06 sec)
create table orders(order_id int auto_increment primary key,customer_id int,order_date date,total_amount decimal(10,2),foreign key (customer_id)     references customers(customer_id));
Query OK, 0 rows affected (0.05 sec)

 desc customers;
+-------------+-------------+------+-----+---------+----------------+
| Field       | Type        | Null | Key | Default | Extra          |
+-------------+-------------+------+-----+---------+----------------+
| customer_id | int         | NO   | PRI | NULL    | auto_increment |
| name        | varchar(50) | YES  |     | NULL    |                |
| email       | varchar(50) | YES  |     | NULL    |                |
+-------------+-------------+------+-----+---------+----------------+
3 rows in set (0.01 sec)

desc orders;
+--------------+---------------+------+-----+---------+----------------+
| Field        | Type          | Null | Key | Default | Extra          |
+--------------+---------------+------+-----+---------+----------------+
| order_id     | int           | NO   | PRI | NULL    | auto_increment |
| customer_id  | int           | YES  | MUL | NULL    |                |
| order_date   | date          | YES  |     | NULL    |                |
| total_amount | decimal(10,2) | YES  |     | NULL    |                |
+--------------+---------------+------+-----+---------+----------------+
4 rows in set (0.01 sec)

insert into customers (name, email) values('Prasad','prasad@gmail.com');
Query OK, 1 row affected (0.02 sec)

insert into customers (name, email) values('Ashok','ashok@gmail.com');
Query OK, 1 row affected (0.01 sec)

insert into customers (name, email) values('Rajesh','rajesh@gmail.com');
Query OK, 1 row affected (0.01 sec)

insert into customers (name, email) values('Krishna','krishna@gmail.com');
Query OK, 1 row affected (0.01 sec)

select * from customers;
+-------------+---------+-------------------+
| customer_id | name    | email             |
+-------------+---------+-------------------+
|           1 | Prasad  | prasad@gmail.com  |
|           2 | Ashok   | ashok@gmail.com   |
|           3 | Rajesh  | rajesh@gmail.com  |
|           4 | Krishna | krishna@gmail.com |
+-------------+---------+-------------------+
4 rows in set (0.00 sec)

insert into orders(customer_id, order_date, total_amount) values(1,'2025-08-15',500);
Query OK, 1 row affected (0.01 sec)

insert into orders(customer_id, order_date, total_amount) values(2,'2025-08-16',1000);
Query OK, 1 row affected (0.01 sec)

insert into orders(customer_id, order_date, total_amount) values(3,'2025-08-17',1500);
Query OK, 1 row affected (0.01 sec)

insert into orders(customer_id, order_date, total_amount) values(4,'2025-08-15',1700);
Query OK, 1 row affected (0.01 sec)

select * from orders;
+----------+-------------+------------+--------------+
| order_id | customer_id | order_date | total_amount |
+----------+-------------+------------+--------------+
|        1 |           1 | 2025-08-15 |       500.00 |
|        2 |           2 | 2025-08-16 |      1000.00 |
|        3 |           3 | 2025-08-17 |      1500.00 |
|        4 |           4 | 2025-08-15 |      1700.00 |
+----------+-------------+------------+--------------+
4 rows in set (0.01 sec)

 CREATE PROCEDURE GetOrdersByCustomer(IN cust_id INT)BEGIN SELECT o.order_id, o.order_date, o.total_amount FROM orders o WHERE o.customer_id = cust_id;
END //
Query OK, 0 rows affected (0.02 sec)
 DELIMITER ;
CALL GetOrdersByCustomer(1);
+----------+------------+--------------+
| order_id | order_date | total_amount |
+----------+------------+--------------+
|        1 | 2025-08-15 |       500.00 |
+----------+------------+--------------+
1 row in set (0.01 sec)

 CREATE FUNCTION CalculateDiscount(order_total DECIMAL(10,2))RETURNS DECIMAL(5,2) DETERMINISTIC BEGIN DECLARE discount DECIMAL(5,2); IF order_total > 500 THEN SET discount = 10.00; ELSEIF order_total > 200 THEN
 SET discount = 5.00; ELSE SET discount = 0.00; END IF; RETURN discount;
    END //
Query OK, 0 rows affected (0.01 sec)

SELECT order_id, total_amount, CalculateDiscount(total_amount) AS discount_percentage FROM orders;
+----------+--------------+---------------------+
| order_id | total_amount | discount_percentage |
+----------+--------------+---------------------+
|        1 |       500.00 |                5.00 |
|        2 |      1000.00 |               10.00 |
|        3 |      1500.00 |               10.00 |
|        4 |      1700.00 |               10.00 |
+----------+--------------+---------------------+
4 rows in set (0.01 sec)
