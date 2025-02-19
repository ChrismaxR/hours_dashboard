---
title: Loonstrook
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

    <LineChart
        data={brutonetto}
        title='Bruto vs. netto salaris'
        x=datum
        y=summed_value
        series=category
        yFmt=eur
        step=false
        markers=true
    />


<Grid cols=2>

    <BarChart
        data={fin_data_bonus}
        title='Bonussen'
        x=datum
        y=value
        series=name
        yFmt=eur
    />

    <BarChart
        data={fin_data_long_out}
        title='Afdrachten'
        x=datum
        y=value
        series=name
        yFmt=eur
    />

</Grid>



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

```sql brutonetto
with nettobruto as (
    select  jaar,
            maand,
            ym,
            datum,
            name, 
            case
                when name in ('netto_salaris') then 'netto'
                else 'bruto' 
            end as category,
            value

    from finhours.fin_long
  	where name in (
        'netto_salaris', 'salaris', 'urenbonus', 'tariefbonus', 
        'vakantiebijslagbonus', 'vakantiebijslag', 'onkosten', 
        'mobiliteitsvergoeding',  'plaatsingsbonus', 'aanbrengbonus'
    )
    and jaar like '${inputs.geselecteerd_jaar.value}'
)

select  jaar,
        maand, 
        ym,
        datum,
        category,
        sum(value) as summed_value
from nettobruto

group by jaar, maand, ym, datum, category

```