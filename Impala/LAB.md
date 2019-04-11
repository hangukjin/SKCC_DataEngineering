# LAB

## 1. Running Queries from the Shell, Scripts, and Hue

### 1.1. Running a Query Using Hue

#### Which customer did your query identify as the winner of the $5,000 prize?

![image](https://user-images.githubusercontent.com/28293389/55932458-b5514d00-5c64-11e9-9c61-fa0d7cf59cb9.png)

> Bridget Burch

### 1.2. Running a Query from the Impala Shell

```impala
> DESCRIBE products;
Query: describe products
+-------------+--------+---------+
| name        | type   | comment |
+-------------+--------+---------+
| prod_id     | int    |         |
| brand       | string |         |
| name        | string |         |
| price       | int    |         |
| cost        | int    |         |
| shipping_wt | int    |         |
+-------------+--------+---------+
Fetched 6 row(s) in 4.91s

> SELECT * FROM products ORDER BY price DESC LIMIT 3;
Query: select * FROM products ORDER BY price DESC LIMIT 3
+---------+------------+-----------------------------------------------+--------+--------+-------------+
| prod_id | brand      | name                                          | price  | cost   | shipping_wt |
+---------+------------+-----------------------------------------------+--------+--------+-------------+
| 1274321 | Byteweasel | Hadoop Cluster, Economy (4-node)              | 975149 | 952934 | 570         |
| 1274308 | Gigabux    | Server (2U rackmount, eight-core, 64GB, 12TB) | 614559 | 556183 | 121         |
| 1274307 | Krustybitz | Server (2U rackmount, eight-core, 64GB, 12TB) | 599319 | 542497 | 121         |
+---------+------------+-----------------------------------------------+--------+--------+-------------+
Fetched 3 row(s) in 0.44s
```
> 1. Hadoop Cluster, Economy (4-node), Byteweasel  
> 2. Server (2U rackmount, eight-core, 64GB, 12TB), Gigabux  
> 3. Server (2U rackmount, eight-core, 64GB, 12TB), Krustybitz

### 1.3. Running a Script in the Impala Shell

#### verify_tablet_order.sql
```sql
SELECT *
FROM orders O
JOIN order_details D
     ON O.order_id = D.order_id
JOIN products P
     ON D.prod_id = P.prod_id
WHERE O.cust_id IN (SELECT cust_id
	                FROM customers
		            WHERE fname LIKE "Bridget"
		                  AND city = "Kansas City")
      AND P.name LIKE '%Tablet%';
```

![image](https://user-images.githubusercontent.com/28293389/55933069-ff3b3280-5c66-11e9-8d38-5da8adeab14c.png)

### 1.4. Running a Query Using Beeline

```SQL
SELECT * FROM products WHERE price < 1000 AND brand = 'Gigabux';
```

![image](https://user-images.githubusercontent.com/28293389/55931968-95b92500-5c62-11e9-825e-8e9cbac2e3a0.png)

