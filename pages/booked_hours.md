---
title:  
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

```sql hours_breakdown

select * from finhours.fin_long
where name in (
     'Educatie',                                        'Part-time',                                        'Vakantieverlof',                                  'Inzet',                                            
     'Intern overleg', 
     'Bijzonder verlof',
     'Nationale feestdag',
     'Ziek',
     'Betaald ouderschapsverlof',
     'Onbetaald ouderschapsverlof',
     'Dokter / tandarts'
)
and jaar like '${inputs.geselecteerd_jaar.value}'

```

<BarChart
    data={hours_breakdown}
    title='Uursoorten per maand'
    x=datum
    y=value
    series=name
    yFmt=num0
/>

<Grid cols=2>
    <AreaChart
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
</Grid>

```sql fin_data_wide
select * from finhours.fin_wide
where jaar like '${inputs.geselecteerd_jaar.value}'
```