---
title:
---
<Alert status=info>
Deze maand: <Value data={datatable} column=datum row=1 fmt='mmm'/>, <Value data={datatable} column=jaar row=1 fmt='yyyy'/>
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
  data={fin_perc_bonus} 
  value=value
  sparkline=datum
  sparklineType=area
  title="% Bonus deze maand"
  fmt=pct1
  comparison=verschil
  comparisonFmt=pct1
  comparisonTitle="vs. vorige maand"
/>
</Grid>

<DataTable data={datatable}>
	<Column id=datum title=" " fmt="mmm yyyy"/>
	<Column id=billable_perc_vorige_maand title="% Billable" fmt=pct1/>
  <Column id=billable_hours_vorige_maand title="# Billable hours" />
  <Column id=bruto_variabel_inkomen title="Bonussen" fmt=eur />
  <Column id=variabel_inkomen_perc title="% Bonussen" fmt=pct1 />
	<Column id=netto_salaris title="Netto salaris" fmt=eur />
</DataTable>


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
where datum > current_date - 330
order by datum desc
```
