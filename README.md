# Insurance-Dashboard-Analysis

# Data Transformation
Data quality issues were identified in the table count of opened and closed policies. Invalid values '1' had to be cleaned up. <br>

Before: <br>
![policies count issue](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count.png)

After: <br>
![cleaned](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count_cleaned.png)

# Concepts Utilized

## DAX Measures

**Total number of policies opened**:
Variables are created to count the number of total policies. The total number of opened policies is also equated to the total number of policies. 
```dax
Policy Count = COUNTROWS(policies)

Policies Opened = [Policy Count]
```
<br>

**Number of policies closed**:
USERELATIONSHIP(): Deactivates the relationship between 'DATE' key and 'INSR_BEGIN' key and activates the relationship between 'DATE' key and 'INSR_END' key to count the number of policies closed. The columns involved are those that define the activated relationship (i.e Policy Count, Date, INSR_END). <br>

The DAX measure below calculates the number of closed policies and using the insurance end dates as the filter context. USERRELATIONSHIP() activates the relationship between column 'Date' in 'Calendar' table and 'Date' column in 'Policies' table.
```dax
Policies Closed = CALCULATE([Policy Count], USERELATIONSHIP(policies[INSR_END],'calendar'[Date]))
```
<br>

**Average Claim where claims were maid on the policy**:
The average claim for claims made is calculated by summing up non-zero values under 'CLAIM_PAID' column and then dividing only by the number of non-zero entries under 'CALIM_PAID' column.
```dax
Avg. Claim_Made = AVERAGE(policies[CLAIM_PAID])
```

<br>

**Average Claim of all opened policies**:
The average claim for all policies that were opened (regardless whether a claim was made) is calculated by adding up all values under 'CLAIM_PAID' column and dividing by the TOTAL number of ROWS. 
This represents a more accurate representation of the average claims per year as it considers all opened policies instead of only cases where a claim was made.
```dax
Avg.Claim_All = DIVIDE([Total Claims],[Policies Opened])
```

Claim Count: Calculates the number of claims that were made out of the total number of policies opened.
```dax
Claim Count = CALCULATE([Policy Count], policies[CLAIM_PAID]>0)
```
<br> 

**Bar Chart Labels**:
Multiple bar chart labels are combined and displayed on each bar. 

v_type: SELECTEDVALUE() is used to pull the name of the vehicle type from the column 'TYPE_VEHICLE' in the policies table. <br>

policies: Format the total number of opened policies for each type of vehicle <br>

p2c: Adds a red dot 🔴 if the Premium-to-Claims ratio is below 1, or green ✅ otherwise. <br>

```dax
Bar Chart Labels = 
    var v_type = SELECTEDVALUE(policies[TYPE_VEHICLE])
    var policies = FORMAT([Policies Opened], "#,##0,.0k")
    var p2c = IF([Premium to Claims Ratio]<1,"🔴","✅")
return
    v_type & " " & policies & " " & p2c
```


# Visualizations

## Scatterplot of Average Premium and Claims by Vehicle Type and Make
Symmetry shading was used to distinguish the Vehicle Type and Make models that were generating profit (purple) from the loss-making vehicle types/makes (red).


## Premium vs Claims Ratio Scatterplot and Matrix Chart
Both charts show which vehicle type/make and usage type would be the most worthwhile to invest in based on the ratio values returned. However, that alone does not justify enough for the company to open more insurance policies in. Tool tips are added to provide information about the number of opened policies, average claim and premium amounts to provide a clearer picture on which vehicle types and makes are worth creating policies in.
