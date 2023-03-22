# PowerBI-Experiment (Work In Progress)
Currently undergoing the documenting process.

# Executive Summary
This report aims to analyse data provided by X Telecom to determine the drivers of customer churn, aid in understanding them, and subsequently inform X Telecom’s decision making. Contents of the report entail analytics, the drivers of customer churn, observations, potential areas for further analysis, and suggestions utilised to inform decision making.

Using Power BI, the report first found the posited assumptions to be invalid. For example, the assumptions of age and yearly call duration being drivers of customer churn is untrue. However, the assumption that net customer spending drives customer churn seems to ring true. Subsequently, the report performed a logistic regression model analysis utilising the dataset, which yielded the most decisive predictor of customer churn, complaints. In addition, other identified impactful drivers are international plan, age groups, call count, subscription length and yearly call duration. This analysis also disproves the earlier assumption of net customer spending correlating to customer churn but requires further analysis for a decisive result.

Next, I identified insights that included areas of improvement and potential areas for data analysis for better understanding. Primarily, I suggest further drilling into attributes such as complaints for complaint types and age groups for missing relationships and splitting the data set by age group for better insights about user types and behaviour.

With our analysis and insights, the report subsequently discusses strategies to address the drivers of customer churn to aid in decision making. For example, to address complaints, Telecom could take a proactive and communicative approach with customers, increase the number of customer surveys, and ensure customers are satisfied with complaint resolutions. Strategies for further drivers are multifaceted and include additional advertisement campaigns for international plan customers and age group specific promotions and deals. 

# Business Context
**X Telecom** is a major Australian mobile network company with business across different countries (i.e., Australia, UK, 
China, USA, Germany). It offers four major plans with a monthly cost between $20-$50 AUD, different 
data rates, and offers to complement plans with an option for international calls.


# Problem statement
**X Telecom** is financially struggling as their customer churn has increased over the past. The churn rate, 
also known as the rate of attrition or customer churn, is the rate at which customers stop doing business 
with an entity. Telecom measures customer churn by looking at whether or not a customer discontinues 
the service with them. If customers churn, it means that they stop doing business with Telecom and 
move to another provider. The churn rate and growth rate are diametrically opposite factors, as the 
former measures the loss of customers and the other measures the acquisition of customers.

# Business Analysis
As a business analyst, I’ve defined the scope of the experiment. I will attempt to understand drivers of customer churn and inform X Telecom in their decision making. This will involve the usage of PowerBI to pit each datasets against another to identify possible relationships and correlations. 

# Data availability
In this experiment case, pretransformed data is provided in multiple formats. I will first merge these data utilizing SSIS for better utilization in the subsequent processes. In the professional enviroment, data utilized for this experiment would be held in the company's secured SQL server. This would further require extraction from said server, subjected to the company's approval. 

# Pre-requisites
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

# Data Extraction, Transformation and Loading
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





# PowerBI Insights
**Descriptive analysis of customer churn**

![Churn Analysis](../main/ImageAssets/PBIChurnAnalysis.png)

![Churn Analysis2](../main/ImageAssets/PBIChurnAnalysis2.png)

Here we begin with a simple Analysis of the dataset by cooperating relevant data into a PowerBI Dashboard. I am looking to investigate two common assumptions of churn, and attempt to uncover if that assumption is valid.

## Assumption 1. Customer Age Drives Churn
This assumption is drawn with the consideration that older users in general like to file more complaints as they become grumpy and then churn.

![Churn by Age](../main/ImageAssets/PBIChurnByAge.png)

![Complaints by Age](../main/ImageAssets/PBIComplaintsByAge.png)

With regards to this assumption, I would disagree. As shown by the visualization named “Churn by Age Group”, we can see that the youngest age group has the highest percentage of churn. 40% of age group 16-20 vs 19% of age group 51-60. With regards to the claim that old people like to file complaints as they become grumpy, we can see from the visualization “Complaints by Age Group” that this is not true as well. After the age group 31-40, we can see a significant drop in complaints percentage per age group, disproving marketing team’s assumption.


## Assumption 2. Customer Behaviour will strongly indate Customer Churn.

This assumption is drawn as (1) people who speak longer on the phone will have a better chance at experiencing how good the company’s service is and are less likely to churn, (2) people who use more data are able to experience the full benefit of the company and are less likely to churn, and that people who spend more money with the company receive the best service and are less likely to churn.

![Churn Predictors](../main/ImageAssets/PBIChurnPredictors.png)

(1) I disagree with assumption of people who speak longer are less likely to churn. As seen in the “Average of Yearly Call Duration (in Mins) by churn visualization, the doughnut chart shows an almost even split between churn and not churn. There is not enough data to draw to this assumption of customer behaviour. Moreover, we need more data to tell if longer call duration equates to higher chance of experiencing the company’s service quality and subsequently affect churn rate.

(2) Figure “Average Weekly Data Usage by Churn” show that average weekly data usage by churned customers are higher than active customers (80.81 vs 78.30). This disproves marketing team’s assumption that correlates more data usage to less likelihood of churn. Regarding more data equating to experiencing the full benefit of the company, there is insufficient data to prove or disprove this part of the assumption.  

(3) As proven by figure “Average Customer Value by Churn Flag”, we can say that customers who spend more money with the company are less likely to churn and therefore this part of the assumption is valid. However, we are unable to quantify and assume that customers who spent more money will receive the best service with the company as there is no data on service quality.


## Tariff Plan Performance

![Churn to Tariff Plans](../main/ImageAssets/PBIChurntoTariff.png)

Attached above is the visualization “Churn Rate by Tariff Plan” derived from the report. This is created to aid X Telcom in valuating the performance of their existing Tariff Plans. The visualization denotes the company’s average churn rate to be 25.53%. As a result, Tariff plan 1 and 2’s product teams need to be concerned about their performance. This is due to their churn rate being higher than the company’s average, at 37.39% and 30.18% respectively. Tariff plan 3 and 4’s product teams are performing better as their churn rate is below the company’s average churn rate of 25.53%, at 18.46% and 16.08% respectively.


# Predictive Analysis of Customer Churn via RapidMiner


![Analytical Process](../main/ImageAssets/RapidMinerAnalyticalProcess)

![Logistic Regression](../main/ImageAssets/RapidMinerLogisticRegression)

![Logistic Regression Performance](../main/ImageAssets/RapidMinerLogisticRegressionPerf)

![Decision Tree Performance](../main/ImageAssets/RapidMinerDecisionTreePerf)




# Business Insights and Strategies

## Report Findings
The findings of the report show that complaints have a strong relationship with churn behaviour. This finding could be a result of complaints not being handled properly. However, we cannot assume that all complaints will result in churn as the data set proves otherwise. Not all customers who complain churn, so we need to do further analysis and more data to find what drives complainants to churn. We would also require more data on the complaint type to theorise the relationship of complaint type to churn. An additional logistic regression analysis on this other data will provide more significant insights when we are trying to rectify this issue. Past year data and further customer feedback can be added to the data pool used for analysis to acquire this data. 

It is also observed that the age group 16-20 churns more than other age groups but does not complain as much (14%). This behaviour could be due to many possibilities such as spending power, susceptibility to change, parents’ decisions, etc. Therefore, we must drill down and complete a hierarchical cluster analysis to find out what decisive data we are missing to investigate this relationship further to aid us in coming up with a solution.

In the earlier PowerBI analysis, an assumption was that customers who spend more money with the company are less likely to churn. Further analysis using logistic regression shows that the net customer spend is an attribute that impacts churn the least. This might be due to correlation and that the net customer spend value utilised in PowerBI is the average sum of the entire customer table. Past data could be included in the analysis further to investigate this for a clearer view of the relationship. Alternatively, splitting the data set by the age group will also clarify user types and behaviour as we cannot assume that spending power is the same across the age groups.

## Churn Contributors
According to the analysis completed earlier in the report, complaints are the most significant driving factor contributing to churn. Even though we may not be able to provide a blanket resolution to address this, the first step that Telecom could take is to pay more attention to resolving customer complaints and ensure that it comes to a satisfactory customer resolution. The company’s current complaint handling process could be inadequate and might need to be revamped. Increasing the speed of handling customer complaints will also reduce customer churn as slow service was highlighted as one of the two main reasons customer churns (Oracle, 2011). Telecom could also take a proactive stance and communicate more with customers to keep in touch with them and receive more feedback. Finding out what the complaints are about will also aid in identifying areas for improvement, and Telecom can strategise further from there. 


The age group is also a factor contributing to churn, particularly the age groups 16-20, 21,30 and 41-50. It is recommended that the company provides promotions and age group targeted deals such as Free Call minutes or loyalty bonuses. These promotions will also help address other churn contributing factors such as yearly call duration, call count and subscription length. It is recommended to further analyse data regarding which promotions would work best with each age group to decide which promotions work best. When developing solutions, research has shown that identifying heterogeneity in the interventions to formulate the proper targeting rules based on the customer’s sensitivity to the company’s actions provides the best efficiency (Ascarza, 2018). One way of doing so is to offer surveys to customers to determine what they would prefer most for analysis and subsequently offer the results-oriented solution.

Earlier analysis had also found that customers with international plan add-ons are less likely to churn. Therefore, we propose more advertising to attract these customers. In addition, Telecom could also introduce promotions for international plan customers with benefits aligned with the other churn contributing factors mentioned above.


# References
Ascarza, E., 2018. Retention futility: Targeting high-risk customers might be ineffective. Journal of Marketing Research, 55(1), pp.80-98.

Oracle. (2011). Customer experience impact report. Retrieved from https://www.oracle.com/us/products/applications/cust-exp-impact-report-epss-1560493.pdf
