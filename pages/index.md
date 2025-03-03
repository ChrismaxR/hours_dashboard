---
title:
---
<Alert status=info>
Deze maand: <Value data={datatable} column=datum row=0 fmt='mmm'/>, <Value data={datatable} column=jaar row=0 fmt='yyyy'/>
</Alert>


<Grid cols=2>
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
  value=verlofuren_label
  title="Vakantieverlof dit jaar"  
  comparison=verlofuren_over
  comparisonTitle="vakantieverlofuren over"
/>
</Grid>

<BarChart
    data={datatable}
    title='Netto Salaris Ontwikkeling'
    x=datum 
    y=netto_salaris
    yFmt=eur>
    <ReferenceLine 
        data={avg_netto} 
        y=avg 
        label="Gemiddelde"
    />
</BarChart>

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
where name = 'billable_perc_vorige_maand'
and verloonde_ym is not null
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

```sql fin_perc_bonus
select datum, 
  		name, 
  		value,
      value - LAG(value) OVER (ORDER BY datum) as verschil
  from finhours.fin_long
where name = 'variabel_inkomen_perc'
and datum > current_date - 365
order by datum desc
```

```sql datatable
select ym, 
       datum,
       jaar,
       billable_perc_vorige_maand, 
       billable_hours_vorige_maand, 
       bruto_variabel_inkomen,
       variabel_inkomen_perc, 
       netto_salaris, 
from finhours.fin_wide
where datum > current_date - 365
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
  select jaar, 
        maand, 
        ym, 
        datum, 
        Vakantieverlof
  from finhours.fin_wide
)

select jaar, 
  sum(vakantieverlof) as verlofuren,
  cast(sum(vakantieverlof) as string) || ' uur' as verlofuren_label,
  144 - verlofuren as verlofuren_over 
from vak
where jaar = cast(extract(year from current_date) as string)
group by jaar
```