# Insurance Financial Analysis 
![Dashboard](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/dashboard_overall.png)

# Contents
* [Objective](#objective)
* [Data Cleaning](#data-cleaning)
* [Data Transformation](#data-transformation)
    * [DAX Measures](#dax-measures)
* [Visualizations](#visualizations)
    * [Average Premium vs Claims by Vehicle Type and Make](#average-premium-vs-claims-by-vehicle-type-and-make)
    * [Policy Claim count by Gender type](#policy-claim-count-by-gender-type)
    * [Claim Amount by Usage Type](#claim-amount-by-usage-type)
    * [Premium to Claim Ratio by Usage Type](#premium-to-claim-ratio-by-usage-type)
    * [Count of opened policies by Vehicle Type](#count-of-opened-policies-by-vehicle-type)
* [Conclusion](#conclusion)

# Objective
This project aims to understand the financial struggles faced by the insurance company EIC and understand what went wrong. <br>

# Data Cleaning
Invalid values were identified in the table count of total opened and closed policies. The invalid values were filtered to be excluded from the analysis. <br>

Before: <br>

![policies count issue](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count.png)

After: <br>

![cleaned](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/policies_count_cleaned.png)

# Data Transformation

## DAX Measures

**Total Claims**: Total amount of claims submitted.
```dax
Total Claims = SUM(policies[CLAIM_PAID])
```

<br>

**Average Premiums Paid by clients**
```dax
Avg.Premium = AVERAGE(policies[PREMIUM])
```

<br>

**Average Claim Amount**: Average claim amount on all opened policies regardless whether a claim was submitted 
```dax
Avg.Claim_All = DIVIDE([Total Claims],[Policies Opened])
```

<br>

**Claim Count**: Total number of claims submitted. Policies where no claims were made are excluded from the count.
```dax
Claim Count = CALCULATE([Policy Count], policies[CLAIM_PAID]>0)
```
<br>

**Total number of policies opened**: Count of total number of opened policies by calculating total number of rows in the dataset. 
```dax
Policy Count = COUNTROWS(policies)

Policies Opened = [Policy Count]
```
<br>

**Number of policies closed**: <br>

USERELATIONSHIP() - Deactivates the relationship between 'DATE' key and 'INSR_BEGIN' key and activates the relationship between 'DATE' key and 'INSR_END' key to count the number of policies closed. The columns involved are those that define the activated relationship (i.e Policy Count, Date, INSR_END). <br>

The DAX measure below calculates the number of closed policies and using the insurance end dates as the filter context. USERRELATIONSHIP() activates the relationship between column 'Date' in 'Calendar' table and 'Date' column in 'Policies' table.
```dax
Policies Closed = CALCULATE([Policy Count], USERELATIONSHIP(policies[INSR_END],'calendar'[Date]))
```
<br>

**Bar Chart Labels**: <br>

Multiple bar chart labels are combined and displayed on each bar. 

v_type: SELECTEDVALUE() is used to pull the name of the vehicle type from the column 'TYPE_VEHICLE' in the policies table. <br>

policies: Format the total number of opened policies for each type of vehicle <br>

p2c: Adds a red dot 🔴 if the Premium-to-Claims ratio is below 1, or green ✅ otherwise.

```dax
Bar Chart Labels = 
    var v_type = SELECTEDVALUE(policies[TYPE_VEHICLE])
    var policies = FORMAT([Policies Opened], "#,##0,.0k")
    var p2c = IF([Premium to Claims Ratio]<1,"🔴","✅")
return
    v_type & " " & policies & " " & p2c
```

<br>

**Adding context to tool tip labels**: <br>

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

A custom tool tip header was created by combining the "Vehicle Type" and "Make" string texts and display it on the tool tip when hovered over the dots in the scatterplot chart of "Premium vs Claim (Average)".
```dax
Tooltip Header = 
    var v_type = SELECTEDVALUE(policies[TYPE_VEHICLE])
    var v_make = SELECTEDVALUE(policies[MAKE])
return
    v_type & " - " & v_make
```

# Visualizations

## Average Premium vs Claims by Vehicle Type and Make
The average premiums and claims of each vehicle type and make are plotted on a scatterplot to determine whether majority of vehicles are generating losses (red) or profits (purple), and this can be viewed yearly using the 'Year' filter.

The tooltip information is enhanced to include information about the number of policies opened for each vehicle type/make. That helps to justify whether policies should be continued for a specific vehicle type/make while considering if the scatter point falls within the profit or loss region denoted by the colours purple and red respectively. <br>

![premium vs claim](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/scatterplot.png)

## Policy Claim count by Gender type
Based on the donut chart shown, Gender Type "0" had opened the most policies as well as also having the most claims as compared to Gender Type "0" and "1". <br>

![Donut Chart](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/donut%20chart.png)

## Claim Amount by Usage Type
The ribbon chart shows the total claim amount of each usage type yearly from 2014 to 2018. It also shows how the rankings of the different usage segments change over the years. <br>

![usage_claim amt_ribbon](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/ribbon%20chart.png)

## Premium to Claim Ratio by Usage Type
The matrix chart shows the premium to claims ratio of the different usage types throughout the years from 2014 to 2018. The colours depict the ratios amount from high (red) to medium (orange/yellow) to low (green).
The chart shows that vehicles with the use type of General Cartage has the lowest overall premium to claim ratio. Given the job nature of land transportation of goods, it could be due to a higher rate of accidents resulting in higher claims being submitted for vehicles in this category. 

On the other hand, "Agricultural Own Farm" has the highest overall premium to claim ratio. This could be due to the low risk nature of the use case in agricultural farming thus resulting in lower claims being submitted.

The results from this plot also tallies with the ribbon chart where it shows "General Cartage" use as the highest ranking in total claim amount every year.

![matrix chart](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/matrix.png)

## Count of opened policies by Vehicle Type
The bar chart below shows the number of opened policies under each vehicle type and Motor-Cycles have the highest number followed by Trucks and Pick-ups. This could be due to businesses having these vehicles as the company vehicles thus having to get a vehicle insurance policy before purchasing one. In each bar, the green and red symbols represent premium-to-claim ratio of more than 1 and less than 1 respectively. <br>

![bar chart](https://github.com/bayyangjie/Insurance-Dashboard-Analysis/blob/main/images/bar%20chart.png)

# Conclusion 
The conclusion summarises the main reasons contributing to the company's financial losses as well as recommendations on areas to focus on for driving profitability.
- Motorcycles have a positive premium-to-claims ratio while also having one of the highest counts of opened policies with the company. In terms of makes of motorcycles, most are also within profit-making ratios (> 1) when comparing average premium to average claims. Thus, with the high count of opened policies and generating positive returns, the company can consider issuing more vehicle policies for motorcycles or create new insurance policy packages. <br>
  
- On the other hand, "Trucks" is one of the vehicle types that has the most number of opened policies as well as having the highest total claim amount per year. Throughout the years, the premium-to-claims ratio for vehicle type is also below one and most Truck models/makes are also in the loss-making region of Average Premium-to-Claims Ratio (red region on scatter plot). Based on these observations, the insurance company could consider stopping issuance or reducing the number of issued vehicle policies for trucks since it is resulting in higher claims than premiums paid. By issuing more vehicle policies further, that could lead to more losses stemming from Trucks.
