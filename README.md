# SQL-Q5
SELECT employee_id, first_name, last_name
FROM employees
WHERE job_title = 'Manager';
SELECT department_id, SUM(salary) AS total_salary_expenditure
FROM employees
GROUP BY department_id;
SELECT employee_id, COUNT(project_id) AS project_count
FROM employee_projects
GROUP BY employee_id
HAVING COUNT(project_id) > 1;
SELECT project_id, project_name, start_date, end_date
FROM projects
WHERE end_date IS NULL OR end_date > CURRENT_DATE;
SELECT job_role, MAX(salary) AS highest_salary
FROM employee_salaries
GROUP BY job_role;
SELECT job_role, MIN(salary) AS lowest_salary
FROM employee_salaries
GROUP BY job_role;
SELECT employee_name, email
FROM employees
WHERE email LIKE '%company%';
SELECT department_name, COUNT(*) AS total_employees
FROM employees
GROUP BY department_name;
SELECT DEPARTMENT_NAME, AVG(SALARY) AS AVERAGE_SALARY
FROM EMPLOYEES
GROUP BY DEPARTMENT_NAME;
SELECT *
FROM EMPLOYEES
WHERE HIRE_DATE < '2020-01-01';
SELECT *
FROM PROJECTS
WHERE DEPARTMENT = 'IT';
SELECT customer_id, product_id, total_revenue
FROM (
    SELECT customer_id, product_id, SUM(revenue) AS total_revenue,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY SUM(revenue) DESC) AS rank
    FROM sales
    GROUP BY customer_id, product_id
) ranked
WHERE rank <= 3;
SELECT c.customer_id, c.customer_name
FROM Customers c
LEFT JOIN Orders o ON c.customer_id = o.customer_id
WHERE o.order_date IS NULL
   OR o.order_date < DATEADD(MONTH, -6, GETDATE())
GROUP BY c.customer_id, c.customer_name
HAVING MAX(o.order_date) IS NULL
   OR MAX(o.order_date) < DATEADD(MONTH, -6, GETDATE());
SELECT c.segment_name, AVG(o.total_amount) AS average_order_value
FROM Orders o
JOIN Customers c ON o.customer_id = c.customer_id
GROUP BY c.segment_name;
SELECT TOP 3 c.customer_id, c.customer_name, SUM(o.total_amount) AS total_spent
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC;
SELECT 
    customer_id, 
    COUNT(DISTINCT YEAR(purchase_date)) AS distinct_years
FROM 
    Purchases
GROUP BY 
    customer_id
HAVING 
    COUNT(DISTINCT YEAR(purchase_date)) >= 3;
SELECT 
    category_name, 
    AVG(quantity) AS average_quantity
FROM 
    Orders
GROUP BY 
    category_name
ORDER BY 
    average_quantity DESC
LIMIT 1;
SELECT 
    customer_id, 
    DATE_FORMAT(order_date, '%Y-%m') AS order_month, 
    COUNT(order_id) AS order_count
FROM 
    Orders
GROUP BY 
    customer_id, 
    DATE_FORMAT(order_date, '%Y-%m')
HAVING 
    COUNT(order_id) > 5;
WITH Q1_Customers AS (
    SELECT 
        customer_id, 
        YEAR(purchase_date) AS purchase_year
    FROM 
        Purchases
    WHERE 
        QUARTER(purchase_date) = 1
    GROUP BY 
        customer_id, 
        YEAR(purchase_date)
),
Q2_Customers AS (
    SELECT 
        customer_id, 
        YEAR(purchase_date) AS purchase_year
    FROM 
        Purchases
    WHERE 
        QUARTER(purchase_date) = 2
    GROUP BY 
        customer_id, 
        YEAR(purchase_date)
)
SELECT 
    Q1.purchase_year AS year,
    COUNT(DISTINCT Q1.customer_id) AS customers_in_Q1,
    COUNT(DISTINCT Q2.customer_id) AS retained_customers_in_Q2,
    (COUNT(DISTINCT Q2.customer_id) * 1.0 / COUNT(DISTINCT Q1.customer_id)) * 100 AS retention_rate
FROM 
    Q1_Customers Q1
LEFT JOIN 
    Q2_Customers Q2
ON 
    Q1.customer_id = Q2.customer_id AND Q1.purchase_year = Q2.purchase_year
GROUP BY 
    Q1.purchase_year;
WITH Q1_Customers AS (
    SELECT 
        customer_id, 
        YEAR(purchase_date) AS purchase_year
    FROM 
        Purchases
    WHERE 
        QUARTER(purchase_date) = 1
    GROUP BY 
        customer_id, 
        YEAR(purchase_date)
),
Q2_Customers AS (
    SELECT 
        customer_id, 
        YEAR(purchase_date) AS purchase_year
    FROM 
        Purchases
    WHERE 
        QUARTER(purchase_date) = 2
    GROUP BY 
        customer_id, 
        YEAR(purchase_date)
)
SELECT 
    Q1.purchase_year AS year,
    COUNT(DISTINCT Q1.customer_id) AS customers_in_Q1,
    COUNT(DISTINCT Q2.customer_id) AS retained_customers_in_Q2,
    (COUNT(DISTINCT Q2.customer_id) * 1.0 / COUNT(DISTINCT Q1.customer_id)) * 100 AS retention_rate
FROM 
    Q1_Customers Q1
LEFT JOIN 
    Q2_Customers Q2
ON 
    Q1.customer_id = Q2.customer_id AND Q1.purchase_year = Q2.purchase_year
GROUP BY 
    Q1.purchase_year;
WITH Q1_Customers AS (
    SELECT 
        customer_id, 
        YEAR(purchase_date) AS purchase_year
    FROM 
        Purchases
    WHERE 
        QUARTER(purchase_date) = 1
    GROUP BY 
        customer_id, 
        YEAR(purchase_date)
),
Q2_Customers AS (
    SELECT 
        customer_id, 
        YEAR(purchase_date) AS purchase_year
    FROM 
        Purchases
    WHERE 
        QUARTER(purchase_date) = 2
    GROUP BY 
        customer_id, 
        YEAR(purchase_date)
)
SELECT 
    Q1.purchase_year AS year,
    COUNT(DISTINCT Q1.customer_id) AS customers_in_Q1,
    COUNT(DISTINCT Q2.customer_id) AS retained_customers_in_Q2,
    (COUNT(DISTINCT Q2.customer_id) * 1.0 / COUNT(DISTINCT Q1.customer_id)) * 100 AS retention_rate
FROM 
    Q1_Customers Q1
LEFT JOIN 
    Q2_Customers Q2
ON 
    Q1.customer_id = Q2.customer_id AND Q1.purchase_year = Q2.purchase_year
GROUP BY 
    Q1.purchase_year;
WITH MonthlyOrders AS (
    SELECT 
        customer_id,
        DATE_TRUNC('month', order_date) AS order_month,
        COUNT(order_id) AS monthly_order_count
    FROM 
        orders
    GROUP BY 
        customer_id, order_month
),
AverageOrders AS (
    SELECT 
        customer_id,
        AVG(monthly_order_count) AS avg_monthly_orders
    FROM 
        MonthlyOrders
    GROUP BY 
        customer_id
)
SELECT 
    m.customer_id,
    m.order_month,
    m.monthly_order_count
FROM 
    MonthlyOrders m
JOIN 
    AverageOrders a
ON 
    m.customer_id = a.customer_id
WHERE 
    m.monthly_order_count > 2 * a.avg_monthly_orders
ORDER BY 
    m.customer_id, m.order_month;
WITH ProductPairs AS (
    SELECT 
        a.product_id AS product_id1,
        b.product_id AS product_id2,
        COUNT(DISTINCT a.order_id) AS order_count
    FROM 
        order_items a
    JOIN 
        order_items b
    ON 
        a.order_id = b.order_id
    WHERE 
        a.product_id < b.product_id
    GROUP BY 
        a.product_id, b.product_id
)
SELECT 
    product_id1,
    product_id2,
    order_count
FROM 
    ProductPairs
WHERE 
    order_count >= 3
ORDER BY 
    order_count DESC;
WITH TotalSold AS (
    SELECT 
        product_id,
        SUM(quantity) AS total_quantity_sold
    FROM 
        order_items
    GROUP BY 
        product_id
),
AverageInventory AS (
    SELECT 
        product_id,
        AVG(quantity_available) AS avg_quantity_available
    FROM 
        inventory
    GROUP BY 
        product_id
)
SELECT 
    t.product_id,
    t.total_quantity_sold / a.avg_quantity_available AS turnover_rate
FROM 
    TotalSold t
JOIN 
    AverageInventory a
ON 
    t.product_id = a.product_id
ORDER BY 
    turnover_rate DESC;
WITH MonthlySales AS (
    SELECT 
        product_id,
        DATE_TRUNC('month', order_date) AS order_month,
        COUNT(order_id) AS monthly_order_count
    FROM 
        orders
    GROUP BY 
        product_id, order_month
),
StandardDeviation AS (
    SELECT 
        product_id,
        STDDEV_POP(monthly_order_count) AS order_stddev
    FROM 
        MonthlySales
    GROUP BY 
        product_id
)
SELECT 
    product_id,
    order_stddev
FROM 
    StandardDeviation
ORDER BY 
    order_stddev ASC;


