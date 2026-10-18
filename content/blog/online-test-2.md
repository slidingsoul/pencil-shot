+++
title = "Online Test 2"
date = "2026-10-18T12:10:58+07:00"

description = "My answers to a software house online test: pseudocode for a number pattern, an ERD and SQL query for a stock trading app, and a Blazor + ASP.NET Core login page."

tags = []
+++

I was looking for a job and got messaged from a *software house*. They asked me about
1. Availability to relocate
2. Notice period
3. Professional Experience

And they proposed to email me an online test and I accepted. Here are the question and answer

## Q1

Write pseudocode for the following case.

N = 5
```
1 
1 2 
2 3 4 
3 4 5 6 
6 7 8 9 10 
```
N = 6
```
1 
1 2 
2 3 4 
3 4 5 6 
6 7 8 9 10 
9 10 1 2 3 4
```
N = 8
```
1 
1 2 
2 3 4 
3 4 5 6 
6 7 8 9 10 
9 10 1 2 3 4 
4 5 6 7 8 9 10 
9 10 1 2 3 4 5 6 
```
N = 10
```
1 
1 2 
2 3 4 
3 4 5 6 
6 7 8 9 10 
9 10 1 2 3 4 
4 5 6 7 8 9 10 
9 10 1 2 3 4 5 6 
6 7 8 9 10 1 2 3 4 
3 4 5 6 7 8 9 10 1 2
```

## Q2

### Scenario

Client XYZ requires an application for recording stock trading activities. Stock trading activities involve customers and stocks.

A customer can buy more than one type of stock in specified quantities, and a customer can also sell stocks they own. Each stock has a purchase price and a selling price applicable to the relevant day.

### Questions

1. Create an Entity-Relationship Diagram (ERD), complete with whatever attributes you consider necessary.
2. Illustrate the table structures and their relationships.
3. Write a query that displays the total stock wealth of each customer.

### Example Output

| No. | Customer Name | Total Stock Wealth |
|---:|---|---:|
| 1 | Yugi | Rp 75,000,000 |
| 2 | Yuuki | Rp 1,000,000 |
| 3 | Yusei | Rp 2,510,200 |

## Q3

Using C#, create a login page containing username and password input fields, a **Login** button, and a **Register** button. Also provide a registration page/form that meets the following requirements.

### 1. Application Requirements

1. The application must be web-based.
2. The application must consist of two parts: a frontend and a backend.
3. The frontend must use Blazor WebAssembly (.NET 8).
4. The backend must use ASP.NET Core Web API (.NET 8).
5. For the frontend, the colors, padding, and margin sizes are flexible. Functionality is the priority.

### 2. Expected Functionality

a. The application must provide two functions: registration and login.

b. The pages must follow the sitemap provided in the original document.

c. The registration process must follow the registration flowchart provided in the original document.

d. The login process must follow the login flowchart provided in the original document.

#### Sitemap
![Sitemap diagram showing the login and register pages](https://i.ibb.co/Hw1Lst7/sitemap.png)
#### Register
![Registration flowchart](https://i.ibb.co/pjb0RLxg/registration.png)
#### Login
![Login flowchart](https://i.ibb.co/gZX2ZHR8/login.png)

---

## Answer

### Q1

```python
N = int(input())
start = 1
end = 0
for i in range(1, N+1):
  for j in range(1, i+1):
    if start > 10:
      start = 1
    print(start, end=" ")
    start += 1 # trailing start
    if i == j:
      if i >= 3:
        if i % 2 == 1: # odd
          end = start - 2
        else: # even
          end = start - 1
      else:
        end = start - 1
  start = end
  print()
```

### Q2

#### ERD
![Entity-relationship diagram of the stock trading application](https://i.ibb.co/tpLprN4m/unnamed.png)
#### Table Structure & Relation
I don't know what the hell is the difference from ERD but anyway

![Table structures and their relationships in the stock trading database](https://i.ibb.co/mCJVqY7P/unnamed2.png)
#### Query
```SQL
WITH holdings AS (
    SELECT
        customer_id,
        stock_id,
        SUM(
            CASE
                WHEN type = 'BUY' THEN quantity
                WHEN type = 'SELL' THEN -quantity
            END
        ) AS quantity_owned
    FROM transactions
    WHERE deleted_at IS NULL
    GROUP BY customer_id, stock_id
),
latest_transactions AS (
    SELECT
        customer_id,
        stock_id,
        price,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id, stock_id
            ORDER BY transaction_date DESC, id DESC
        ) AS rn
    FROM transactions
    WHERE deleted_at IS NULL
)
SELECT
    c.id AS customer_id,
    c.name AS customer_name,
    SUM(h.quantity_owned * lt.price) AS total_wealth
FROM holdings h
JOIN latest_transactions lt
    ON lt.customer_id = h.customer_id
    AND lt.stock_id = h.stock_id
    AND lt.rn = 1
JOIN customers c
    ON c.id = h.customer_id
WHERE h.quantity_owned > 0
  AND c.deleted_at IS NULL
GROUP BY
    c.id,
    c.name
ORDER BY
    c.id;
```

### Q3
Here's the [zip file](https://drive.google.com/file/d/14rrZ144RDClihb7Y1OnfFqBDNoM3eCIZ/view?usp=sharing). Just ignore the `.env` in the `/backend` ok?