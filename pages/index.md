---
title: Inkomsten dashboard
---

```sql jaar_selector
select 
    jaar
from finhours.fin_wide

group by 1
```

<Dropdown
    name=geselecteerd_jaar
    data={jaar_selector}
    value=jaar
>
    <DropdownOption value="%" valueLabel="All Items"/>
</Dropdown>

## Netto Inkomen

<BarChart
    data={fin_data_wide}
    title='Netto inkomsten per maand'
    x=datum
    y=netto_salaris
    yFmt=eur
/>

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
select * from finhours.fin_wide
where jaar like '${inputs.geselecteerd_jaar.value}'
```

```sql fin_data_long
select * from finhours.fin_long

```

```sql fin_data_bruto

select datum, sum(value) as bruto_bedrag 
 from (
  select * 
    from finhours.fin_long
   where name in (
    'salaris', 'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'vakantiebijslag',
    'onkosten', 'mobiliteitsvergoeding',  'plaatsingsbonus', 'aanbrengbonus'
  )
 )
where jaar like '${inputs.geselecteerd_jaar.value}'
group by datum

```

```sql fin_data_long_out
select * from finhours.fin_long
where name in (
   'leaseauto', 'pensioen', 'inhoudingen', 'loonheffing'
)
and jaar like '${inputs.geselecteerd_jaar.value}'
```

```sql fin_data_bonus
select * from finhours.fin_long
where name in (
   'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'aanbrengbonus', 'plaatsingsbonus'
)
and jaar like '${inputs.geselecteerd_jaar.value}'
```

