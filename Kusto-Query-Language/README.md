# KQL: Kusto Query Language

Kusto Query Language is a powerful tool to explore your data and discover patterns, identify anomalies and outliers, create statistical modeling, and more. The query uses schema entities that are organized in a hierarchy similar to SQLs: databases, tables, and columns.

**What is a Kusto query?** </br>
A Kusto query is a read-only request to process data and return results. The request is stated in plain text, using a data-flow model that is easy to read, author, and automate. Kusto queries are made of one or more query statements.</br>

**Used for:** </br>
1. To Query Log Analytics - Events </br>
2.	To Query Windows Defender </br>
3.	To Query Sentinel </br></br>

**Documentation:** https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/ </br>
**Practice KQL:** 
1.	https://aka.ms/lademo </br>
2.	https://dataexplorer.azure.com/clusters/help/databases/Samples </br>

**Operators:**
>	**between** </br>

    StormEvents 
  	| where StartTime between (datetime(2007-11-01) .. datetime(2007-12-01))

>	**count** </br>
	
    StormEvents  
  	| count

>	**take** </br>
	To get a sense of the data, use the take operator to view a sample of records. This operator returns a specified number of arbitrary rows from the table, which can be useful for previewing the general data structure and contents. </br>


  	StormEvents 
  	| take 5 

>	**Select a subset of columns** </br>
	Use the project operator to simplify the view and select a specific subset of columns. Using project is often more efficient and easier to read than viewing all columns. </br>

  	StormEvents 
  	| take 5 
  	| project State, EventType, DamageProperty 

>	**Get distinct data** </br>
	
    SecurityEvent
  	| where Channel == "Security" 
  	| distinct Activity 

>	**Filter Data** </br>
	
    SecurityEvent 
  		| where Channel == "Security" 
  		| limit 10 
    
  	SecurityEvent
  		| where Channel == "Security" 
  		| where Activity contains "created" 
  		| limit 10 

>	**Sort Result** </br>
	
    StormEvents
  	| where State == 'TEXAS' and EventType == 'Flood' 
  	| sort by DamageProperty 
  	| project StartTime, EndTime, State, EventType, DamageProperty 
	
>	**Get Top 'N' Entries** </br>
	The top operator returns the first n rows sorted by the specified column.

  	StormEvents
  	| where State == 'TEXAS' and EventType == 'Flood'
  	| top 5 by DamageProperty
  	| project StartTime, EndTime, State, EventType, DamageProperty
	
>	**Get 'N' Entries** </br>
  
  	SecurityEvent
  	| limit 10

>	**TimeRange** </br>
  
  	SecurityEvent
  	| where TimeGenerated > ago(3d)
  	| order by TimeGenerated asc
  	| limit 10

>	**Create calculated columns** </br>
	The project and extend operators can both create calculated columns. </br>
	**Note:** Use project to specify only the columns you want to view, and use extend to append the calculated column to the end of the table. </br>
  	
  	StormEvents
  	| where State == 'TEXAS' and EventType == 'Flood'
  	| top 5 by DamageProperty desc
  	| project StartTime, EndTime, Duration = EndTime - StartTime, DamageProperty
  	
  	StormEvents
  	| where State == 'TEXAS' and EventType == 'Flood'
  	| top 5 by DamageProperty desc
  	| extend Duration = EndTime - StartTime

>	**Summerize Operator** </br>
	The summarize operator is essential to performing aggregations over your data. The summarize operator groups together rows based on the by clause and then uses the provided aggregation function to combine each group in a single row. </br>
  	
  	StormEvents
  	| summarize TotalStorms = count() by State

>	**Map values from one set to another** </br>
	Static mapping is a useful technique for changing the presentation of your results. In KQL, one way to perform static mapping is by using a dynamic dictionary and accessors to map values from one set to another. </br>

   
  	let x = dynamic ( {
                      "JBOX00" : "First",
                      "JBOX10": "Second"
                       });

  	ProtectionStatus
  	| take 100
  	| where Computer in ("JBOX00","JBOX10")
  	| summarize count() by Computer
  	| project Machine = x[Computer], Total = count_


>	**Visualize query results** </br>
	Visualizing query results in a chart or graph can help you identify patterns, trends, and outliers in your data. You can do this with the render operator. </br></br>
  **Other Charts:** areachart, barchart, columnchart, piechartscatterchart, table, timechart, treemap </br>
	**Default:** table </br>
 
  	StormEvents
  	| summarize TotalStorms = count() by State
  	| render barchart

>	**Conditionally count rows** </br>
	When analyzing your data, use countif() to count rows based on a specific condition to understand how many rows meet the given criteria. </br>
	
  	StormEvents
  	| summarize StormsWithCropDamage = countif(DamageCrops > 0) by State
  	| top 5 by StormsWithCropDamage

>	**Group data into bins** </br>
	To aggregate by numeric or time values, you'll first want to group the data into bins using the bin() function. Using bin() can help you understand how values are distributed within a certain range and make comparisons between different periods. </br>
	The following query counts the number of storms that caused crop damage for each week in 2007. The 7d argument represents a week, as the function requires a valid timespan value. </br>
  	
  	StormEvents
  	| where StartTime between (datetime(2007-01-01) .. datetime(2007-12-31)) 
  		and DamageCrops > 0
  	| summarize EventCount = count() by bin(StartTime, 7d)
	
>	**Calculate the min, max, avg, and sum** </br>

  	StormEvents
  	| where DamageCrops > 0
  	| summarize
  		MaxCropDamage=max(DamageCrops), 
  		MinCropDamage=min(DamageCrops), 
  		AvgCropDamage=avg(DamageCrops)
  		by EventType
  	| sort by AvgCropDamage
	
>	**Calculate percentage based on two columns** </br>
  
  	StormEvents
  	| summarize 
  		TotalStormsInState = count(),
  		StormsWithPropertyDamage = countif(DamageProperty > 0)
  		by State
  	| extend PercentWithPropertyDamage = 
  		round((todouble(StormsWithPropertyDamage) / TotalStormsInState * 100), 2)
  	| sort by StormsWithPropertyDamage

> **Scalar Function** </br>
	The toscalar() function to convert the tabular result of the count() function to a scalar value. </br>
	
  	let TotalStorms = toscalar(StormEvents | summarize count());

>	**Extract unique values** </br>
	Use make_set() to turn a selection of rows in a table into an array of unique values. </br>
  	
  	StormEvents
  	| where DeathsDirect > 0 or DeathsIndirect > 0
  	| summarize StormTypesWithDeaths = make_set(EventType) by State
  	| project State, StormTypesWithDeaths
  	| sort by array_length(StormTypesWithDeaths)

>	**Switch: Bucket data by condition** </br>
	The case() function groups data into buckets based on specified conditions. The function returns the corresponding result expression for the first satisfied predicate, or the final else expression if none of the predicates are satisfied. </br>	
	This example groups states based on the number of storm-related injuries their citizens sustained. </br>
  	
  	StormEvents
  	| summarize InjuriesCount = sum(InjuriesDirect) by State
  	| extend InjuriesBucket = case (
  								  InjuriesCount > 50,
  								  "Large",
  								  InjuriesCount > 10,
  								  "Medium",
  								  InjuriesCount > 0,
  								  "Small",
  								  "No injuries"
  							  )
  	| sort by State asc
	
>	**Use the join operator** </br>
		
  	StormEvents
  	| summarize PropertyDamage = sum(DamageProperty) by State
  	| join kind=innerunique PopulationData on State
  	| project State, PropertyDamagePerCapita = PropertyDamage / Population
  	| sort by PropertyDamagePerCapita
  	
  	StormEvents
  	| where EventType == "Lightning"
  	| distinct State
  	| join kind=inner (
  		StormEvents 
  		| where EventType == "Avalanche"
  		| distinct State
  		)
  		on State
