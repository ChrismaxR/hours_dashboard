---
title: Inkomsten dashboard
---

```sql fin_data
select * from fin_data

```

```sql fin_data_bruto

select datum, sum(value) as bruto_bedrag 
 from (
  select * 
    from fin_data_long
   where name in (
    'gross_salary', 'hour_bonus', 'tariff_bonus', 'holiday_bonus', 'holiday',
    'expenses', 'mobility_allowance',  'plaatsingsbonus', 'aanbrengbonus'
  )
 )
group by datum

```

```sql fin_data_long_out
select * from fin_data_long
where name in (
   'car', 'pension', 'inhouding'
)
```

```sql fin_data_bonus
select * from fin_data_long
where name in (
   'hour_bonus', 'tariff_bonus', 'holiday_bonus', 'aanbrengbonus', 'plaatsingsbonus'
)
```
