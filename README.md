# Maven Market Grocery Analysis

It's a grocery stores sales project of Maven Market. The data for this project is from Maven Market, a multi-national grocery chain with locations in Canada, Mexico and the United States.

## Problem Statement 
This report helps the Maven Market, a multi-national grocery chain with locations in Canada, Mexico and the United States understand their customers better. It also lets them know the sales
performance by using this report, so they can further work on factors to increase their sales.

## Steps Followed 
I have worked through the entire business intelligence workflow: connecting and shaping the data, building a relational model, adding calculated fields, and designing an interactive report.

### PART 1: Connecting & Shaping the Data

- Step 1 : Load data into Power BI Desktop, dataset is a csv file.
- Step 2 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options.
- Step 3 : Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
- Step 4 : Name the tables, and make sure that headers have been promoted, confirm that data types are accurate.
- Step 5 : Created the new columns, calculated columns and conditional columns and combined the queries via append method.

Snap of the shaped data in Power Query Editor:

![image](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/652bcd4e-4aae-47ee-a90d-de1fd30efc2f)

### PART 2: Creating the Data Model


- Step 6 : In the MODEL view, arrange the tables with the lookup tables above the data tables.
  - Connect **Transaction_Data** to **Customers**, **Products**, and **Stores** using valid primary/foreign keys
  - Connect **Transaction_Data** to **Calendar** using both date fields, with an inactive "stock_date" relationship

  - Connect **Return_Data** to **Products**, **Calendar**, and **Stores** using valid primary/foreign keys

  -  Connect **Stores** to **Regions** as a *snowflake* schema

- Step 7 : Confirm the following:

  - All relationships follow one-to-many cardinality, with primary keys (1) on the lookup side and foreign keys (*) on the data side

  - Filters are all one-way (no two-way filters) and filter context flows "downstream" from lookup tables to data tables

  - Data tables are connected via shared lookup tables (not directly to each other). 

Snap of tables in Model View:

![image](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/e4d6323f-82c4-4008-b2ef-d17c4eeb7efe)

### PART 3: Adding DAX Measures

- Step 8 : In the DATA view, add the following **calculated columns**:

- In the **Calendar** table,

```Weekend = IF('Calendar'[Day Name] in {"Saturday", "Sunday"}, "Y", "N")```

```End of Month = EOMONTH('Calendar'[date],0)```

- In the **Customers** table,

```Current Age = DATEDIFF(Customers[birthdate], TODAY(),YEAR)```

``` Priority = IF(Customers[homeowner] = "Y" && Customers[member_card] = "Golden", "High", "Standard")```

```Short Country = UPPER(LEFT(Customers[customer_country],3))```

```House no. = LEFT(Customers[customer_address], SEARCH(" ", Customers[customer_address]) - 1)```

- In the **Products** table, 

```
Price Tier = SWITCH(TRUE(), Products[product_retail_price] > 3, "High", Products[product_retail_price] > 1, "Mid", "Low")
```

- In the **Stores** table, 

```Year Since Remodeled = DATEDIFF(Stores[last_remodel_date], TODAY(),YEAR)```


- Step 9 : In the REPORT view, add the following **measures**

- Created a separate table for all the measures

```Quantity Sold = SUM('Transactions Data'[quantity])```

```Quantity Returned = SUM(Return_Data[quantity])```

```Total Transactions = COUNTROWS('Transactions Data')```

```Total Returns = COUNTROWS(Return_Data)```

```Return Rate = DIVIDE([Quantity Returned], [Quantity Sold])```

```% Weekend Transaction = DIVIDE(CALCULATE([Total Transactions], 'Calendar'[Weekend] = "Y"), [Total Transactions])```

```All Transactions = COUNTROWS(ALL('Transactions Data'))```

```All Returns = COUNTROWS(ALL(Return_Data))```

```Total Revenue = SUMX('Transactions Data', 'Transactions Data'[quantity]*RELATED(Products[product_retail_price]))```

```Total Cost = SUMX('Transactions Data', 'Transactions Data'[quantity]*RELATED(Products[product_cost] ))```

```Total Profit = [Total Revenue]- [Total Cost]```

```Profit Margin = DIVIDE([Total Profit], [Total Revenue])```

```Unique Product = DISTINCTCOUNT(Products[product_name])```

```YTD Revenue = TOTALYTD([Total Revenue], 'Calendar'[date])```

```60 - Day Revenue = CALCULATE([Total Revenue],DATESINPERIOD('Calendar'[date],MAX('Calendar'[date]), -60, DAY))```

```Last Month Transactions = CALCULATE([Total Transactions], PREVIOUSMONTH('Calendar'[date]))```

```Last Month Revenue = CALCULATE([Total Revenue], PREVIOUSMONTH('Calendar'[date]))```

```Last Month Profit = CALCULATE([Total Profit], PREVIOUSMONTH('Calendar'[date]))```

```Last Month Returns = CALCULATE([Total Returns], PREVIOUSMONTH('Calendar'[date]))```

```Revenue Target = [Last Month Revenue]*1.05```



### PART 4: Building the Report
- Step 10 : Inserted the Maven Market logo

![Maven Market  Logo](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/80d17ca4-40d8-42e7-8f47-9063d2424ad3)

- Step 11 : Inserted a **Matrix** visual to show **Total Transactions**, **Total Profit**, **Profit Margin**, and **Return Rate** by **Product_Brand**
(Add a visual level Top N filter to only show the top 30 product brands, then sort descending by Total Transactions)

![Screenshot 2024-03-27 235606](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/c35f8e7b-3036-484a-b6ab-12a1c7f2b797)

- Step 12 : Added a **KPI Card** to show **Total Transactions** (named as *Current Month Transactions*), with **Start of Month** as the trend axis and **Last Month Transactions** as the target goal,  **Total Profit** (as *Current Month Profit*) (vs. Last month Profit) and one for **Total Returns** (named as *Current Month Returns*) (vs. Last Month Returns).

![Screenshot 2024-03-28 000912](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/7cb54210-c27d-47f3-b6dd-90c43b1203d2)

- Step 13 : Added a **Map** visual to show **Total Transactions** by store city

![Screenshot 2024-03-28 002839](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/e42d76c0-ef3b-4486-b8e7-992d8d19be00)

- Step 14: Add a **Treemap** visual to break down **Total Transactions** by store country (Pull in store_state and store_city beneath store_country in the "Group" field to enable **drill-up and drill-down** functionality)

![Screenshot 2024-03-28 003013](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/bd3e3846-b388-4159-ab9d-f0341a753dc1)

- Step 15: Beneath the map, add a **Column Chart** to show **Total Revenue** by week, and format as you see fit

![Screenshot 2024-03-28 003256](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/959b9a79-2df3-4dc5-9e1d-328f8c2ea9cf)

- Step 16: Add a **Gauge Chart** to show **Total Revenue** against **Revenue Target** (as either "target value" or "maximum value")

![Screenshot 2024-03-28 003321](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/9570d0b1-8f36-4a54-b608-f8669bcd88c4)

 
# Report Snapshot (Power BI Desktop)

 ![Screenshot 2024-03-28 004108](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/675c6b26-22de-4a5f-a655-566542f7c9e0)

# Insights

![Screenshot 2024-03-28 003928](https://github.com/Vishal7999/Maven_Market_Analysis/assets/145573983/f0fb2077-5cf6-455f-b2fc-2ddc99348140)
