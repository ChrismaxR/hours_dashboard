---
title: Inkomsten dashboard
---


## Netto Inkomen

<BarChart
    data={fin_data}
    title='Netto inkomsten per maand'
    x=datum
    y=net_salary
    yFmt=eur>
    <ReferenceArea xMin='2022-01-01' xMax='2022-12-31' label=2022 color=#092635/>
    <ReferenceArea xMin='2023-01-01' xMax='2023-12-31' label=2023 color=#9EC8B9/>
    <ReferenceArea xMin='2024-01-01' xMax='2024-12-31' label=2024 color=#092635/>
    <ReferenceArea xMin='2025-01-01' xMax='2025-12-31' label=2025 color=#9EC8B9/>
</BarChart>

## Loonstrook details

<BarChart
    data={fin_data_bruto}
    title='Loonstrook - Bruto inkomsten'
    x=datum
    y=bruto_bedrag
    yFmt=eur
/>

<BarChart
    data={fin_data_long_out}
    title='Loonstrook - afdrachten'
    x=datum
    y=value
    series=name
    yFmt=eur
/>

<BarChart
    data={fin_data_bonus}
    title='Loonstrook - bonus'
    x=datum
    y=value
    series=name
    yFmt=eur
/>





## Billabillity

<BarChart
    data={fin_data}
    title='Billable % per month'
    x=datum
    y=billable_perc_prev_month
    yFmt=pct0
/>

<BarChart
    data={fin_data}
    title='Billable hours per month'
    x=datum
    y=billable_hours_prev_month
/>



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
