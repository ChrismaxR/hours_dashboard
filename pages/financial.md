---
title:  
---

<Dropdown
    name=geselecteerd_jaar
    data={jaar_selector}
    value=jaar
    multiple=true
    defaultValue='2025'
/>


<LineChart
    data={brutonetto}
    title='Bruto vs. netto salaris'
    x=datum
    y=summed_value
    series=category
    yFmt=eur
    step=false
    markers=true
    markerShape=emptyCircle>
        <ReferenceLine 
            data={avg_brutonetto} 
            y=Netto 
            label="Gem. Netto" 
            color=#27445D 
            labelPosition="aboveStart"
        />
        <ReferenceLine 
            data={avg_brutonetto} 
            y=Bruto 
            label="Gem. Bruto" 
            color=#27445D 
            labelPosition="aboveStart"
        />
</LineChart>


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
        colorPalette={[
            '#fcdad9',
            '#e88a87',
            '#eb5752',
            '#cf0d06',
        ]}
    />

</Grid>

```sql jaar_selector
select 
    jaar
from finhours.fin_wide

group by 1
```

```sql fin_data_wide
select * from finhours.fin_wide
where jaar in ${inputs.geselecteerd_jaar.value}
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
where jaar in ${inputs.geselecteerd_jaar.value}
group by datum

```

```sql fin_data_long_out
select * from finhours.fin_long
where name in (
   'leaseauto', 'pensioen', 'inhoudingen', 'loonheffing'
)
and jaar in ${inputs.geselecteerd_jaar.value}
```

```sql fin_data_bonus
select * from finhours.fin_long
where name in (
   'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'aanbrengbonus', 'plaatsingsbonus'
)
and jaar in ${inputs.geselecteerd_jaar.value}
```

```sql brutonetto
with nettobruto as (
    select  jaar,
            maand,
            ym,
            datum,
            name, 
            case
                when name in ('netto_salaris') then 'Netto'
                else 'Bruto' 
            end as category,
            value

    from finhours.fin_long
  	where name in (
        'netto_salaris', 'salaris', 'urenbonus', 'tariefbonus', 
        'vakantiebijslagbonus', 'vakantiebijslag', 'onkosten', 
        'mobiliteitsvergoeding',  'plaatsingsbonus', 'aanbrengbonus'
    )
    and jaar in ${inputs.geselecteerd_jaar.value}
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

```sql avg_brutonetto

WITH nettobruto AS (
    SELECT  
        jaar,
        maand,
        ym,
        datum,
        name, 
        CASE
            WHEN name IN ('netto_salaris') THEN 'Netto'
            ELSE 'Bruto' 
        END AS category,
        value
    FROM finhours.fin_long
    WHERE name IN (
        'netto_salaris', 'salaris', 'urenbonus', 'tariefbonus', 
        'vakantiebijslagbonus', 'vakantiebijslag', 'onkosten', 
        'mobiliteitsvergoeding',  'plaatsingsbonus', 'aanbrengbonus'
    )
    AND jaar in ${inputs.geselecteerd_jaar.value}
),

agg1 AS (
    SELECT  
        jaar,
        maand, 
        ym,
        datum,
        category,
        SUM(value) AS summed_value
    FROM nettobruto
    GROUP BY jaar, maand, ym, datum, category
),

avg_values AS (
    SELECT 
        category, 
        AVG(summed_value) AS avg_summed_value 
    FROM agg1 
    GROUP BY category
)

SELECT * 
FROM avg_values
PIVOT (
    any_value(avg_summed_value) FOR category IN ('Netto', 'Bruto')
)
```