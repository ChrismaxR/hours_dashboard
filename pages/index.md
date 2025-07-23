---
title:
---

<Grid cols=3>
  <BigValue 
    data={fin_agg_netto} 
    value=value
    sparkline=datum
    sparklineType=area
    title="Netto salaris deze maand"
    fmt=eur
    comparison=verschil
    comparisonFmt=eur
    comparisonTitle="vs. vorige maand"
  />

  <BigValue 
    data={datatable} 
    value=uurloon
    sparkline=datum
    sparklineType=area
    title="Uurloon deze maand"
    fmt=eur2
    comparison=uurloon_verschil
    comparisonFmt=eur2
    comparisonTitle="vs. vorige maand"
  />

  <BigValue 
    data={fin_agg_bill_perc} 
    value=value
    sparkline=datum
    sparklineType=area
    title="Billable % deze maand"
    fmt=pct1
    comparison=verschil
    comparisonFmt=pct1
    comparisonTitle="vs. vorige maand"
  />

  <BigValue 
    data={fin_agg_bonus} 
    value=value
    sparkline=datum
    sparklineType=area
    title="Bonus deze maand"
    fmt=eur
    comparison=verschil
    comparisonFmt=eur
    comparisonTitle="vs. vorige maand"
  />

  <BigValue 
    data={vakantieuren} 
    value=label
    title="Vakantieverlof dit jaar"  
    comparison=vakantie_uren_over
    comparisonTitle="vakantieverlofuren over"
  />
</Grid>

<LineChart
    data={datatable}
    title='Netto Salaris Ontwikkeling'
    x=datum 
    y=netto_salaris
    yFmt=eur
    markers=true
    markerShape=emptyCircle>
    <ReferenceLine 
        data={avg_netto} 
        y=avg 
        label="Gem."
        color=#27445D
        labelPosition="aboveStart"
    />
</LineChart>


Laatste update: <Value data={update_time} column=update row=0 fmt='longdate'/>



```sql fin_agg_netto
select datum, 
  		name, 
  		value,
      value - LAG(value) OVER (ORDER BY datum) as verschil
  from finhours.fin_long
where name = 'netto_salaris'
and datum > current_date - 365
order by datum desc
```

```sql fin_agg_bill_perc
 select datum,
  	    value,
        value - LAG(value) OVER (ORDER BY datum) as verschil
    from finhours.fin_long
   where 1 = 1
     and name = 'billable_perc_vorige_maand'
     and datum > current_date - 365
order by datum desc
```

```sql fin_agg_bonus
select datum, 
  		name, 
  		value,
      value - LAG(value) OVER (ORDER BY datum) as verschil
  from finhours.fin_long
where name = 'bruto_variabel_inkomen'
and datum > current_date - 365
order by datum desc
```


```sql datatable
with dt as (
  select ym, 
        datum,
        jaar,
        billable_perc_vorige_maand, 
        billable_hours_vorige_maand, 
        bruto_variabel_inkomen,
        variabel_inkomen_perc, 
        netto_salaris, 
        netto_salaris/lag(billable_hours_vorige_maand) over (order by datum) as uurloon
  from finhours.fin_wide
  where datum > current_date - 365
  order by datum desc
)

select dt.*, 
       uurloon - lag(uurloon) over (order by datum) as uurloon_verschil
from dt
order by datum desc
```

```sql avg_netto
select avg(netto_salaris) as avg
from (
  select * from ${datatable}
)

```

```sql vakantieuren
with vak as (
  select substring(gewerkte_ym, 1, 4) as gewerkte_jaar, 
        maand, 
        gewerkte_ym, 
        datum, 
        Vakantieverlof
  from finhours.fin_wide

  where gewerkte_jaar = EXTRACT(YEAR FROM CURRENT_DATE)
)

select gewerkte_jaar,
  sum(Vakantieverlof) as vakantie_uren, 
  cast(sum(vakantieverlof) as string) || ' uur' as label,
  144 - vakantie_uren as vakantie_uren_over
  
  from vak
 group by gewerkte_jaar
```

```sql update_time
select max(update_date_time) as update from finhours.source_data_meta
```
