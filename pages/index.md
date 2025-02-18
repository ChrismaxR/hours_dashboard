---
title: Entrador Dashboard
---

<BigValue 
  data={fin_agg_netto} 
  value=value
  sparkline=datum
  title="Netto salaris deze maand"
  fmt=eur
  comparison=verschil
  comparisonFmt=eur
  comparisonTitle="vs. vorige maand"
  row=0
/>

<BigValue 
  data={fin_agg_bill_perc} 
  value=value
  sparkline=datum
  title="Billable % deze maand"
  fmt=pct0
  comparison=verschil
  comparisonFmt=pct0
  comparisonTitle="vs. vorige maand"
  row=0
/>

<BigValue 
  data={fin_agg_bonus} 
  value=value
  sparkline=datum
  title="Bonus deze maand"
  fmt=eur
  comparison=verschil
  comparisonFmt=eur
  comparisonTitle="vs. vorige maand"
  row=1
/>

<BigValue 
  data={fin_agg_vast} 
  value=value
  sparkline=datum
  title="Vast inkomen deze maand"
  fmt=eur
  comparison=verschil
  comparisonFmt=eur
  comparisonTitle="vs. vorige maand"
  row=1
/>


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
  		name, 
  		value,
      value - LAG(value) OVER (ORDER BY datum) as verschil
  from finhours.fin_long
where name = 'billable_perc_vorige_maand'
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

```sql fin_agg_vast
select datum, 
  		name, 
  		value,
      value - LAG(value) OVER (ORDER BY datum) as verschil
  from finhours.fin_long
where name = 'bruto_vast_inkomen'
and datum > current_date - 365
order by datum desc
```


