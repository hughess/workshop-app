---
title: San Francisco 311 Cases
---


<!--
ISSUES
- Are nulls not getting counted as zero on days where there are no cases for a category? Seems to min out at 1, looks wrong
- What is right time grain to use throughout app? Daily, weekly, monthly?
- Include links to individual records on SF 311 Site (assuming conf. wifi good enough)
-->


<!-- START OF SECTION 1 -->

<!-- 1. Add your first query
    - Name it 'description'
    - Calculate min_date, max_date, and case count  
-->

```sql description
select min(opened) as min_date, max(opened) as max_date, count(1) as count
from sf311.cases
```

<!-- 2. Add a Details component to explain the dataset
    - Use the Value component to display the min date, max date, and record count in the Details componet
    - Add a nested Details component inside to display this explanation of 311 cases:
          311 is the city's request service for non-emergencies. Citizens can report local issues to 311 and monitor the status of the request. Each case represents something that was reported through this service.
--> 

<Details title='About this data'>
  This dataset includes <Value data={description} column=count fmt=num0k/> 311 cases in San Francisco from <Value data={description} column=min_date fmt=shortdate/> to <Value data={description} column=max_date fmt=shortdate/>.

  <Details title='What are 311 Cases?'>
    311 is the city's request service for non-emergencies. Citizens can report local issues to 311 and monitor the status of the request. Each case represents something that was reported through this service.
  </Details>

</Details>


<!-- 3. Add some BigValues
    - Add a

-->


```sql summary
select count(1) as total_cases, count(1) filter (status = 'Open') as open_cases
from sf311.cases
```

<BigValue
  data={summary}
  value=total_cases
  fmt=num0k
/>

<BigValue
  data={summary}
  value=open_cases
  fmt=num0k
/>


<!-- 4. Add a trend line chart -->

```sql trend
select date_trunc('day', opened) as date,
count(1) as cases
from sf311.cases
where ${inputs.dimensions}
group by all
order by date asc
```


<LineChart
  title="Case Trend"
  data={trend}
  x=date
  y=cases
  yAxisTitle=cases
/>

<!-- 5. Add a DimensionGrid -->

```sql cases
select 
  responsible_agency,
  category,
  request_type,
  source
from sf311.cases 
```

<DimensionGrid data={cases} name=dimensions/>

<!-- 7. Add a CalendarHeatmap -->

 <CalendarHeatmap
  title="Daily 311 Cases"
  data={trend}
  date=date
  value=cases
/>


<!-- END OF SECTION 1 -->


<!-- START OF SECTION 2 -->

<!-- 1. Change your dimension grid to become an input by using the 'name' prop -->

<!-- 2. Then hook this up to the LineChart by adding a 'where' clause to your trend query -->

<!-- 3. Add the input to the chart title so you get the context when the chart updates -->

<!-- END OF SECTION 2 -->