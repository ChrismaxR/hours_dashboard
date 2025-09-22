---
title: More things to worry about
---

<Dropdown
    name=geselecteerd_jaar
    data={jaar_selector}
    value=jaar
    multiple=true
    selectAllByDefault=true
/>

<Grid cols=4>
  <BigValue 
    data={total_bruto} 
    value=bruto_bedrag
    title="Totaal Bruto verdiend"
    fmt=eur
  />

  <BigValue 
    data={total_netto} 
    value=netto_bedrag
    title="Totaal Netto verdiend"
    fmt=eur
  />

  <BigValue 
    data={total_netto} 
    value=billed_hours
    title="Totaal Billable uren"
  />

  <BigValue 
    data={total_netto} 
    value=netto_per_billed_hour
    title="Netto per billed uur"
    fmt=eur
  />
</Grid>

<LineChart
    data={perc_variable_inc}
    title='Percentage inkomen uit Bonussen per maand'
    x=datum
    y=variabel_inkomen_perc
    yFmt=pct0
    markers=true
    markerShape=emptyCircle>
    <ReferenceLine
        data={perc_variable_inc_avg}
        y=avg_variabel_inkomen_perc
        label=Gem.
        color=#27445D
        labelPosition="aboveStart"
    />
</LineChart>


```sql jaar_selector
select 
    jaar
from finhours.fin_wide

group by 1
```

```sql perc_variable_inc
select datum, 
        variabel_inkomen_perc
  from finhours.fin_wide 
  where 1=1 
    and jaar in ${inputs.geselecteerd_jaar.value}

```

```sql perc_variable_inc_avg
select avg(variabel_inkomen_perc) as avg_variabel_inkomen_perc
  from finhours.fin_wide
 where 1=1
   and jaar in ${inputs.geselecteerd_jaar.value}
```

```sql total_bruto
select sum(value) as bruto_bedrag 
 from (
  select * 
    from finhours.fin_long
   where 1 = 1
     and name in (
        'salaris', 'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'vakantiebijslag',
        'onkosten', 'mobiliteitsvergoeding',  'plaatsingsbonus', 'aanbrengbonus'
        )
     and jaar in ${inputs.geselecteerd_jaar.value}
 )
```

```sql total_netto
select sum(netto_salaris) as netto_bedrag, 
       sum(billable_hours_vorige_maand) as billed_hours, 
       sum(netto_salaris)/sum(billable_hours_vorige_maand) as netto_per_billed_hour
from finhours.fin_wide

where 1=1
  and jaar in ${inputs.geselecteerd_jaar.value}
```