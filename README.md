
# Income Tax Filing Tracker

This repository contains SQL code to track income tax filing statuses of users, whether they filed late or completely missed filing for a given financial year.

## SQL Code

The SQL code provided in this repository helps in identifying users who have either filed their income tax returns late or missed filing completely. The query utilizes two tables: `income_tax_dates` and `users`.

![day20](https://github.com/bhumikadata/Income-Tax-Return/assets/131578649/c794bc8b-cfde-40b5-920f-d8c0490ed711)

```sql
with all_users as (
    select 
      u.user_id,
      i.financial_year,
      i.file_due_date
    from 
      users u
    cross join 
      income_tax_dates i    
    group by 
      u.user_id,
      i.financial_year,
      i.file_due_date 
)
select 
  a.user_id,
  a.financial_year,
  case 
    when u.return_file_date > a.file_due_date then 'late return' 
    else 'missed'
  end as comment
from 
  all_users a
left join 
  users u 
on a.user_id = u.user_id and a.financial_year = u.financial_year   
where 
  u.return_file_date > a.file_due_date or u.return_file_date is null;
```


## Explanation

## Common Table Expression (CTE):

The CTE all_users is used to generate a list of all possible combinations of user_id, financial_year, and file_due_date. This is achieved through a cross join between the users table and the income_tax_dates table, ensuring that every user is paired with every financial year.
The purpose of this CTE is to create a baseline dataset that includes every possible filing scenario for each user in each financial year.

## SELECT Statement:

The SELECT statement then joins the all_users CTE with the users table using the user_id and financial_year columns.
It calculates the comment based on a conditional expression within a CASE statement:
If the return_file_date of a user is greater than the file_due_date, it indicates a late return.
Otherwise, it indicates a missed return.
The result is a list of users along with the financial year for which they have either filed late returns or completely missed filing.

## WHERE Clause:

The WHERE clause filters the results to include only cases where the return_file_date is greater than the file_due_date or where the return_file_date is null (indicating a missed filing).
