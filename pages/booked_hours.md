---
title:  
---


<Dropdown
    name=geselecteerd_jaar
    data={jaar_selector}
    value=jaar
    multiple=true
    selectAllByDefault=true
/>



<BarChart
    data={hours_breakdown}
    title='Uursoorten per maand'
    x=gewerkte_datum
    y=hours
    series=name_filter
    yFmt=num0
/>


<LineChart
    data={fin_data_wide}
    title='Billable % per maand'
    x=gewerkte_datum
    y=facturabele_perc_vorige_maand
    yFmt=pct0
    markers=true
    markerShape=emptyCircle>
    <ReferenceLine
        data={bill_avg}
        y=bill_perc_avg
        label=Gem.
        color=#27445D
        labelPosition="aboveStart"
    />
</LineChart>

<BarChart
    data={fin_data_wide}
    title='Billable hours per maand'
    x=datum
    y=facturabele_uren_vorige_maand>
    <ReferenceLine
        data={bill_avg}
        y=bill_avg
        label=Gem.
        color=#27445D
        labelPosition="aboveStart"
    />
</BarChart>


```sql jaar_selector
select 
    jaar
from finhours.fin_wide

group by 1
```

```sql hours_breakdown

select gewerkte_datum,
  	   name_filter, 
       sum(value) as hours
  
 from finhours.fin_long
where 1=1 
and name_filter != 'No filter'
and value > 0
and extract(year from gewerkte_datum) in ${inputs.geselecteerd_jaar.value}

group by 
   gewerkte_datum, 
   name_filter
  
order by 
   gewerkte_datum, 
   name_filter


```

```sql fin_data_wide
select * from finhours.fin_wide
where extract(year from gewerkte_datum) in ${inputs.geselecteerd_jaar.value}
```

```sql bill_avg
select avg(facturabele_uren_vorige_maand) as bill_avg,
       avg(facturabele_perc_vorige_maand) as bill_perc_avg 
  from finhours.fin_wide
 where 1=1
 and jaar in ${inputs.geselecteerd_jaar.value}
and datum < (SELECT MAX(datum) FROM finhours.fin_wide)
```