**Query #1**
```sql
SELECT 
      week_date, 
      TO_CHAR(TO_DATE(week_date, 'DD/MM/YY'), 'Day') AS day_of_week
    FROM 
      data_mart.weekly_sales;

