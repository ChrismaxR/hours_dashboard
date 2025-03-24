---
title:  
---


<Dropdown
    name=geselecteerd_jaar
    data={jaar_selector}
    value=jaar
>
    <DropdownOption value="%" valueLabel="All Items"/>
</Dropdown>


<BarChart
    data={hours_breakdown}
    title='Uursoorten per maand'
    x=datum
    y=value
    series=name
    yFmt=num0
/>

<Grid cols=2>
    <LineChart
        data={fin_data_wide}
        title='Billable % per maand'
        x=datum
        y=billable_perc_vorige_maand
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
        y=billable_hours_vorige_maand>
        <ReferenceLine
            data={bill_avg}
            y=bill_avg
            label=Gem.
            color=#27445D
            labelPosition="aboveStart"
        />
    </BarChart>
</Grid>

```sql jaar_selector
select 
    jaar
from finhours.fin_wide

group by 1
```

```sql hours_breakdown

select * from finhours.fin_long
where 1=1 
and name in (
     'educatie',                                        'vakantieverlof',                                  'inzet',                                            
     'intern_overleg', 
     'bijzonder_verlof',
     'nationale_feestdag',
     'ziek',
     'betaald_ouderschapsverlof',
     'onbetaald_ouderschapsverlof',
     'dokter_tandarts'
)
and jaar like '${inputs.geselecteerd_jaar.value}'
and datum < (SELECT MAX(datum) FROM finhours.fin_long)

```

```sql fin_data_wide
select * from finhours.fin_wide
where jaar like '${inputs.geselecteerd_jaar.value}'
and datum < (SELECT MAX(datum) FROM finhours.fin_wide)
```

```sql bill_avg
select avg(billable_hours_vorige_maand) as bill_avg,
       avg(billable_perc_vorige_maand) as bill_perc_avg 
  from finhours.fin_wide
 where 1=1
 and jaar like '${inputs.geselecteerd_jaar.value}'
and datum < (SELECT MAX(datum) FROM finhours.fin_wide)
```