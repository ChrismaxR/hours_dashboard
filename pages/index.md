---
title: Inkomsten dashboard
---

## Netto Inkomen

<BarChart
    data={fin_data_wide}
    title='Netto inkomsten per maand'
    x=datum
    y=netto_salaris
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
    data={fin_data_wide}
    title='Billable % per maand'
    x=datum
    y=billable_perc_vorige_maand
    yFmt=pct0
/>

<BarChart
    data={fin_data_wide}
    title='Billable hours per maand'
    x=datum
    y=billable_hours_vorige_maand
/>



```sql fin_data_wide
select * from fin_data_wide

```

```sql fin_data_long
select * from fin_data_long

```

```sql fin_data_bruto

select datum, sum(value) as bruto_bedrag 
 from (
  select * 
    from fin_data_long
   where name in (
    'salaris', 'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'vakantiebijslag',
    'onkosten', 'mobiliteitsvergoeding',  'plaatsingsbonus', 'aanbrengbonus'
  )
 )
group by datum

```

```sql fin_data_long_out
select * from fin_data_long
where name in (
   'leaseauto', 'pensioen', 'inhoudingen', 'loonheffing'
)
```

```sql fin_data_bonus
select * from fin_data_long
where name in (
   'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'aanbrengbonus', 'plaatsingsbonus'
)
```
