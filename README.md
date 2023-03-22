# PowerBI-Experiment (Work In Progress)
Currently undergoing the documenting process.

## Business Context
**X Telecom** is a major Australian mobile network company with business across different countries (i.e., Australia, UK, 
China, USA, Germany). It offers four major plans with a monthly cost between $20-$50 AUD, different 
data rates, and offers to complement plans with an option for international calls.


## Problem statement
**X Telecom** is financially struggling as their customer churn has increased over the past. The churn rate, 
also known as the rate of attrition or customer churn, is the rate at which customers stop doing business 
with an entity. Telecom measures customer churn by looking at whether or not a customer discontinues 
the service with them. If customers churn, it means that they stop doing business with Telecom and 
move to another provider. The churn rate and growth rate are diametrically opposite factors, as the 
former measures the loss of customers and the other measures the acquisition of customers.
Attempt to understand drivers of their customer churn and inform their decision making. 

## Business Analysis
xx

## Data availability
xx

## Pre-requisites
Apart from the pre-installed Microsoft Office Suite, we need to install 2 important softwares.

1. **Power BI**, an open source Data Visualization software created by Microsoft as part of the Microsoft Business Intelligence Toolkit.
    - Download Power BI.
    - Step by step installation guide is available.


2. **Microsft SQL Server**, a relational database management system developed by Microsoft is used for Data Storage, Retrieval and data transformation.
    - Download the SQL Server 2019 Developer version
    - SQL Server 2019 Developer Step by step installation guide.


3. **Microsoft SQL Server Management Studio**, a software application first launched with Microsoft SQL
    - Download SQL Server Management Studio 18.10
    - SQL Server Management Studio - Step by step installation guide.

4. **Microsoft SQL Server Integration Services**, a component of the Microsoft SQL Server database software that can be used to perform a broad range of data migration tasks.
    - Download SQL Server Integration Services
    - SQL Server Integration Services - Step by step installation guide available.

5. **RapidMiner**, a data science platform designed by RapidMiner for enterprises that analyses the collective impact of organizations’ employees, expertise and data.
    - Download RapidMiner
    - Step by step installation guide available.

## Data Extraction, Transformation and Loading
**SSIS**
Extract data from the provided datasets (Customer1, Customer2, & Data MobilePlans).
Utilize SSIS to transform data. 

**SSIS Data Transformation**

![Dimension 1](../main/ImageAssets/SSISDataTrf.png)

This required some modifications to the datasets to workaround certain shortcomings.
> -	Ensure Data Type of flat file get data matches for connection managers Customer1 and 2. 
> -	Add “Splitting CC & CID” after Flat File Get Data (Customer2)
>     -	Expression 1: CountryCode: (DT_I2) LEFT([CountryCode&CustomerId],FINDSTRING([CountryCode&CustomerId] ,"+",1) - 1)
>     -	Expression 2: CustomerID: (DT_I2)  RIGHT([CountryCode&CustomerId] ,LEN([CountryCode&CustomerId]) - FINDSTRING([CountryCode&CustomerId] ,"+",1))
> -	Configure “Union All” Input 2 mapping to derived CustomerID and add “CountryCode” input
> -	Add “Script Component” after “Sort by CID” to add “Customer Key” (database preparation) 
> -	Configure “Save Customer Dimension File” and map connection manager CustomerDimension
> -	Ensure Data Type of flat file get data matches for connection manager MobilePlans
> -	Add “TarrifPlanKey from PlanID” after flat file get data,
> -	Expression 1: TarrifPlanKEY: PlanID
> -	Add “Sort by TarrifPlanKey” to sort data according to TarrifPlanKey
> -	Configure “Save Product Dimension File” to include TarrifPlanKEY.
> -	Map connection manager ProductDimension
> -	Add “Multicast” after “Union All”
> -	Add “TarrifPlanKEY from Tariff Plan” after “Multicast”
> -	Expression 1: TariffPlanKEY: [Tariff Plan]
> -	Add “Sort2 by TarrifPlanKEY” to prepare for merge join
> -	Add “Merge Join” after “Sort2 by TarrifPlanKey”
> -	Edit “Merge Join” with left outer join as join type, select “TariffPlanKey” as the join key and select CustomerId and PlanId columns for fact table.
> -	Add “Save Fact Table File” and map connection manager “FactTable”.

This, will result in the following:

**Customer Table (Dimension 1)**

![Dimension 1](../main/ImageAssets/DataViewerCustomerTable.png)

**Mobile Plan Product Table (Dimension 2)**

![Mobile Plan Product Table](../main/ImageAssets/DataViewerPlanProductTable.png)

**Fact Table**

![Fact Table](../main/ImageAssets/DataViewerFactTable.png)





**PowerBI**


**RapidMiner**



##Creating Visualizations
xx

## Business Insights and Strategies

**Report Findings**

The findings of the report show that complaints have a strong relationship with churn behaviour. This finding could be a result of complaints not being handled properly. However, we cannot assume that all complaints will result in churn as the data set proves otherwise. Not all customers who complain churn, so we need to do further analysis and more data to find what drives complainants to churn. We would also require more data on the complaint type to theorise the relationship of complaint type to churn. An additional logistic regression analysis on this other data will provide more significant insights when we are trying to rectify this issue. Past year data and further customer feedback can be added to the data pool used for analysis to acquire this data. 

It is also observed that the age group 16-20 churns more than other age groups but does not complain as much (14%). This behaviour could be due to many possibilities such as spending power, susceptibility to change, parents’ decisions, etc. Therefore, we must drill down and complete a hierarchical cluster analysis to find out what decisive data we are missing to investigate this relationship further to aid us in coming up with a solution.

In the earlier PowerBI analysis, an assumption was that customers who spend more money with the company are less likely to churn. Further analysis using logistic regression shows that the net customer spend is an attribute that impacts churn the least. This might be due to correlation and that the net customer spend value utilised in PowerBI is the average sum of the entire customer table. Past data could be included in the analysis further to investigate this for a clearer view of the relationship. Alternatively, splitting the data set by the age group will also clarify user types and behaviour as we cannot assume that spending power is the same across the age groups.

**Churn Contributors**

According to the analysis completed earlier in the report, complaints are the most significant driving factor contributing to churn. Even though we may not be able to provide a blanket resolution to address this, the first step that Telecom could take is to pay more attention to resolving customer complaints and ensure that it comes to a satisfactory customer resolution. The company’s current complaint handling process could be inadequate and might need to be revamped. Increasing the speed of handling customer complaints will also reduce customer churn as slow service was highlighted as one of the two main reasons customer churns (Oracle, 2011). Telecom could also take a proactive stance and communicate more with customers to keep in touch with them and receive more feedback. Finding out what the complaints are about will also aid in identifying areas for improvement, and Telecom can strategise further from there. 


The age group is also a factor contributing to churn, particularly the age groups 16-20, 21,30 and 41-50. It is recommended that the company provides promotions and age group targeted deals such as Free Call minutes or loyalty bonuses. These promotions will also help address other churn contributing factors such as yearly call duration, call count and subscription length. It is recommended to further analyse data regarding which promotions would work best with each age group to decide which promotions work best. When developing solutions, research has shown that identifying heterogeneity in the interventions to formulate the proper targeting rules based on the customer’s sensitivity to the company’s actions provides the best efficiency (Ascarza, 2018). One way of doing so is to offer surveys to customers to determine what they would prefer most for analysis and subsequently offer the results-oriented solution.

Earlier analysis had also found that customers with international plan add-ons are less likely to churn. Therefore, we propose more advertising to attract these customers. In addition, Telecom could also introduce promotions for international plan customers with benefits aligned with the other churn contributing factors mentioned above.


## References
Ascarza, E., 2018. Retention futility: Targeting high-risk customers might be ineffective. Journal of Marketing Research, 55(1), pp.80-98.

Oracle. (2011). Customer experience impact report. Retrieved from https://www.oracle.com/us/products/applications/cust-exp-impact-report-epss-1560493.pdf
