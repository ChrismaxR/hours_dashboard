---
title: Booked hours
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

## Booked hours
```sql hours_breakdown

select * from finhours.fin_long
where name in (
     'educatie',                                        'part_time',                                        'vakantieverlof',                                  'inzet',                                            'intern_overleg',                                  'bijzonder_verlof',                                 'nationale_feestdag',                              'ziek',                                             'betaald_ouderschapsverlof_christian_van_gaalen',  'onbetaald_ouderschapsverlof_christian_van_gaalen', 'dokter_tandarts'
)
and jaar like '${inputs.geselecteerd_jaar.value}'

```

<BarChart
    data={hours_breakdown}
    title='Uursoorten per maand'
    x=datum
    y=value
    series=name
/>