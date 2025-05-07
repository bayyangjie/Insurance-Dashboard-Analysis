# Insurance-Dashboard-Analysis
This analysis aims to discover what were the reasons that contributed to the losses of an insurance company. 
![Dashboard](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/dashboard_overall.png)

# Data Transformation
Data quality issues were identified in the table count of opened and closed policies. Invalid values '1' had to be cleaned up. <br>

Before: <br>
![policies count issue](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count.png)

After: <br>
![cleaned](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count_cleaned.png)

# DAX Measures

**Total Claims**:
Total amount of claims submitted.
```dax
Total Claims = SUM(policies[CLAIM_PAID])
```

<br>

**Average Premiums Paid by Clients**:
```dax
Avg.Premium = AVERAGE(policies[PREMIUM])
```

<br>

**Average Claim Amount of all opened policies**:
This calculates the average claim amount regardless whether a claim was submitted on the policy or not. 
```dax
Avg.Claim_All = DIVIDE([Total Claims],[Policies Opened])
```

<br>

**Claim Count**:
Total number of claims submitted. Policies where zero claims were made are excluded from the count.
```dax
Claim Count = CALCULATE([Policy Count], policies[CLAIM_PAID]>0)
```
<br>

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

<br>

**Creating context to tool tip labels**:
In the Premium vs Claim (Averages) scatter plot, the tooltip information was further enhanced by adding more information about what vehicle type and make each dot represents. 
The following DAX expression was used to display the vehicle type and make in addition to the information about the vehicle make's policy.
```dax
Tooltip Header = 
    var v_type = SELECTEDVALUE(policies[TYPE_VEHICLE])
    var v_make = SELECTEDVALUE(policies[MAKE])
return
    v_type & " - " & v_make
```
<br>

**Custom Tooltip Header**
A custom tool tip header was created by combining the names of the vehicle type and make into a string to be shown on the tool tip when hovered over the dots in the scatterplot chart of "Premium vs Claim (Average)".
```dax
Tooltip Header = 
    var v_type = SELECTEDVALUE(policies[TYPE_VEHICLE])
    var v_make = SELECTEDVALUE(policies[MAKE])
return
    v_type & " - " & v_make
```

# Visualizations

## Average Premium vs Claims by Vehicle Type and Make (Scatterplot)
The average premiums and claims of each vehicle type and make are plotted on a scatterplot to determine whether majority of vehicles are generating losses (red) or profits (purple), and this can be viewed yearly using the 'Year' filter.

The tooltip information is enhanced to include more information about the number of policies opened for each vehicle type/make. That helps in further justifying whether policies should still be continued for a specific vehicle type/make coupled with the location of the scatter point in the chart. <br>

![premium vs claim](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/scatterplot.png)

## Policy Claim count by Gender type
Gender Type "0" had opened the most policies as well as also having the most claims as compared to Gender Type "0" and "1". <br>

![Donut Chart](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/donut%20chart.png)

## Claim Amount by Usage Type
The chart shows the claim amount of each usage type for each year from 2014 to 2018. It also shows how the rankings of the different usage segments change over the years. <br>

![usage_claim amt_ribbon](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/ribbon%20chart.png)
