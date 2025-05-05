# Insurance-Dashboard-Analysis

# Data Transformation
Data quality issues were identified in the table count of opened and closed policies. Invalid values '1' had to be cleaned up. <br>

Before: <br>
![policies count issue](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count.png)

After: <br>
![cleaned](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count_cleaned.png)

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

The average claim for claims made is calculated by summing up non-zero values under 'CLAIM_PAID' column and then dividing only by the number of non-zero entries under 'CALIM_PAID' column.
```dax
Avg. Claim_Made = AVERAGE(policies[CLAIM_PAID])
```

The average claim for all policies that were opened (regardless whether a claim was made) is calculated by adding up all values under 'CLAIM_PAID' column and dividing by the TOTAL number of ROWS.
```dax
Avg.Claim_All = DIVIDE([Total Claims],[Policies Opened])
```
