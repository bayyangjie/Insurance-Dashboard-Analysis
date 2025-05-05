# Insurance-Dashboard-Analysis



# Concepts Utilized

## DAX Measures
Variables are created to count the number of total policies. The total number of opened policies is also equated to the total number of policies. 
```dax
Policy Count = COUNTROWS(policies)

Policies Opened = [Policy Count]
```


USERELATIONSHIP(): Deactivates the relationship between 'DATE' key and 'INSR_BEGIN' key and activates the relationship between 'DATE' key and 'INSR_END' key to count the number of policies closed. The columns involved are those that define the activated relationship (i.e Policy Count, Date, INSR_END).

The DAX measure below calculates the number of closed policies and using the insurance end dates as the filter context.
```dax
Policies Closed = CALCULATE([Policy Count], USERELATIONSHIP(policies[INSR_END],'calendar'[Date]))
```

