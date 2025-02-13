---
title: Data Viewer
---

```sql fin_data_wide
select * from fin_data_wide

```

```sql fin_data_long
select * from fin_data_long

```

```sql fin_data_bruto

select datum, sum(value) as bruto_bedrag 
 from (
  select * 
    from fin_data_long
   where name in (
    'salaris', 'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'vakantiebijslag',
    'onkosten', 'mobiliteitsvergoeding',  'plaatsingsbonus', 'aanbrengbonus'
  )
 )
group by datum

```

```sql fin_data_long_out
select * from fin_data_long
where name in (
   'leaseauto', 'pensioen', 'inhoudingen', 'loonheffing'
)
```

```sql fin_data_bonus
select * from fin_data_long
where name in (
   'urenbonus', 'tariefbonus', 'vakantiebijslagbonus', 'aanbrengbonus', 'plaatsingsbonus'
)
```