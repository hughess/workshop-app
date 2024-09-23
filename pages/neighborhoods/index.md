<!-- START OF SECTION 3 -->

##### Neighborhood Explorer

{#if inputs.map_input.neighborhood === true}

    # All Neighborhoods

{:else}

    # {inputs.map_input.neighborhood}

    [See neighborhood deep dive &rarr;](./{inputs.map_input.neighborhood})

{/if}

```sql categories
select 
  date_trunc('day', opened) as date,
  status,
  count(1) as cases
from sf311.cases
group by all
order by date 
```

```sql comparison_stats
select
    upper(neighborhood) as neighborhood,
    count(1) as cases,
    count(1) filter (status = 'Closed') / count(1) as close_rate
from sf311.cases
group by all
order by cases desc
```

```sql neighborhood_selector
select neighborhood, count(1) as cases
from sf311.cases
where neighborhood is not null
group by all
order by cases desc
```

```sql filtered_trend
select date_trunc('week', opened) as date,
count(1) as cases
from sf311.cases
where opened <= '2024-08-31'
and (neighborhood ilike '${inputs.map_input.neighborhood}'
or '${inputs.map_input.neighborhood}' = 'true')
group by all
order by date
```

```sql filtered_cases
select * from sf311.cases
where (neighborhood ilike '${inputs.map_input.neighborhood}'
or '${inputs.map_input.neighborhood}' = 'true')
and latitude <> 0
order by opened desc
limit 100
```


<LineBreak lines=1/>


<Grid cols=2>

<Group>

### Neighborhood Selector

<AreaMap
  data={neighborhood_selector}
  geoJsonUrl='https://evd-geojson.b-cdn.net/sf_hoods.geojson'
  geoId=name
  areaCol=neighborhood
  value=cases
  name=map_input
/>
</Group>


<Group>

### Weekly Case Trend

<LineChart
    data={filtered_trend}
    x=date
    y=cases
    chartAreaHeight=280
/>

</Group>

</Grid>


### Category Breakdown

```sql category_table
select
    category,
    count(1) as cases
from sf311.cases
where neighborhood ilike '${inputs.map_input.neighborhood}'
or '${inputs.map_input.neighborhood}' = 'true'
group by all
order by cases desc
```

<DataTable data={category_table}>
    <Column id=category/>
    <Column id=cases contentType=colorscale/>
</DataTable>



## Neighborhood List

```sql neighborhood_links
select REPLACE(upper(neighborhood), '/', '%2F') as neighborhood, sum(cases) as cases
from ${neighborhood_selector}
group by all
```



<DataTable data={neighborhood_links} link=neighborhood>
    <Column id=neighborhood/>
    <Column id=cases contentType=colorscale/>
</DataTable>


