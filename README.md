# SQL-Water-Corp-Project
SQL and Data Visualization Hands-On using Water Corporation Dataset (Water Consumption)
Raw Data:
[WaterCorporationProjectt.xls](https://github.com/AjokeAishat712747/SQL-Water-Corp-Project/files/13694875/WaterCorporationProjectt.xls)

Aim: To Clean,Analyze and visualize Water Corporation Dataset (Water Consumption) which shows usage and supply of water to client.
Process: 
1. Excel Data was loaded using the Microsoft SQL Server Managment Studio.
2. Reviewed and understood the Dataset (we identified 5 dimension tables and 2 fact tables)
3. Created Primary key for all the tables(This was done manually using design option and Auto incrementa was set)
   
Cleaning was done in the Azure Data Studio:
1.  Updated all the Foriegn key on the fact table as shown below ( All foriegn key on the fact table were replaced by corresponding Primary key on dimension tables)
   
/*---Upadte foreign key in facility fact table**
set qc.FacilityID=f.FacilitySk
from wcp.Qualities as qc
     INNER JOIN WCP.Facilities as f on  f.FacilityID=qc.FacilityID
where f.latestrecord ='Y'

---Upadte foreign key in consumption fact table**
Update c
set c.FacID=f.FacilitySk,c.CustID=cu.CustomerSk, c.PropID=p.PropertySk , c.CustomerType= ct.CustomerTypeSK
FROM wcp.Consumptions as c
     INNER JOIN WCP.Facilities as f on  f.FacilityID=c.FacID
     INNER JOIN WCP.Customers as cu on  cu.CustomerID=c.CustID
     INNER JOIN WCP.Properties as p on  p.PropertyID=c.PropID
     INNER JOIN WCP.CustomerTypes as ct on  ct.CustomerType=c.CustomerType
where f.latestrecord ='Y' and cu.LatestRecord= 'Y'
*/

2. All Foriegn key constriant on the fact table were link to corresponding Primary key on dimension tables using Edit data on Azure Data Studio

Facts tables shown below.
   
[Results.xlsx](https://github.com/AjokeAishat712747/SQL-Water-Corp-Project/files/13694862/Results.xlsx)

[Resultsq.xlsx](https://github.com/AjokeAishat712747/SQL-Water-Corp-Project/files/13694866/Resultsq.xlsx)


---Questions and Solutions

What is the name of Customer with ID "CUST0002"?
What is the Average Turbidity for each facility in the first 6 months of 2023?
Which Facility recorded the highest Chlorine level in the month of April?
Which month recorded the highest water consumption?
Which Customer Type consumes the least amount of water?
How much water does "Big Bear Treatment Plant" supply to "City of Edmonton"; broken down by each month?
Which Facility recorded consumption 95% of its maximum capacity and for how many consecutive months?
How much water does "Neighbourhood 1" receives from "Jane Doe Treatment Plant" in March 2023?
Rank the Customers based on their total Water Consumption


--What is the name of Customer with ID "CUST0002"?---
/*
SELECT FirstName, LastName ,Fullname from wcp.Customers
where CustomerID ='Cust0002'
*/

![Q1](https://github.com/AjokeAishat712747/SQL-Water-Corp-Project/assets/139535267/2c4c6de9-6b03-4230-8226-e4b3b23fe33e)

--what is the Average Turbidity for each facility in the first 6 months of 2023?--
/*
Select f.Name, f.FacilityID, ROUND(AVG(q.TurbidityLvl),2) as "Average Turbidity" from wcp.Qualities as q
 inner join wcp.Facilities as f on f.FacilitySK= q.FacilitySk
Where q.date BETWEEN '2023-01-01' and '2023-06-30'
Group by f.FacilityID, f.NAME
*/

![Q2](https://github.com/AjokeAishat712747/SQL-Water-Corp-Project/assets/139535267/d0dfbc72-922d-48fc-9f56-24b9a864a2a4)

---Which Facility recorded the highest Chlorine level in the month of April?--
/*
Select f.Name, ROUND(Max(q.ChlorineLvl),2) as "Highest Chlorine Level" from wcp.Qualities as q
 inner join wcp.Facilities as f on f.FacilitySK= q.FacilitySk
Where DATEPART(Month, q.[Date]) = 4
Group by f.FacilityID, f.NAME
order by 2 DESC;
*/

![Q3](https://github.com/AjokeAishat712747/SQL-Water-Corp-Project/assets/139535267/354e93a0-3791-4124-97e8-a7e6b2d34881)

---Which month recorded the highest water consumption?--
/*
Select Sum(c.ConsumptionLtr) as 'Total consumption per month', DATENAME(MONTH, c.Date) as 'monthname'
from wcp.Consumptions as c
group by DATENAME(Month, c.[Date])
order by 'Total consumption per month' DESC
*/

---Which Customer Type consumes the least amount of water?--
/*
SELECT ct.value as 'Sector Name', ct.CustomerType , Sum(c.consumptionLtr) as "Water Consumption per Type"
from wcp.Consumptions as c
INNER JOIN wcp.CustomerTypes as ct on ct.CustomerTypeSK=c.CustomerTypeSk
Group by ct.CustomerType,ct.value
ORDER by "Water Consumption per Type"
*/

--How much water does "Big Bear Treatment Plant" supply to "City of Edmonton"; broken down by each month?--
/*
Select Sum(c.ConsumptionLtr) as 'Water supply per Month', DATENAME(MONTH, c.Date) as 'monthname'
      from wcp.Consumptions as c
          INNER JOIN wcp.Customers as cd on cd.CustomerSK=c.CustomerSk
          INNER JOIN wcp.Facilities as f on f.FacilitySK=c.FacilitySk
          WHERE cd.City= 'Edmonton' and f.Name='Big Bear Treatment Plant'
          Group by DATENAME(MONTH, c.Date)
          ORDER by 1 DESC;
*/

---which Facility recorded consumption 95% of its maximum capacity and for how many consecutive months?--
PS:CTE was used
/*
With Btt as 
(Select f.name, f.MaxiumCapacity,  Sum(c.ConsumptionLtr) as 'Watersupply',DATENAME(MONTH, c.Date) as 'monthname' 
      from wcp.Consumptions as c
          INNER JOIN wcp.Facilities as f on f.FacilitySK=c.FacilitySk
          Group by DATENAME(MONTH, c.Date),f.name, f.MaxiumCapacity) 
Select btt.name,btt.monthname, ROUND(Sum((btt.Watersupply/btt.MaxiumCapacity)*100),2) as "supply per capacity "
from Btt
Group by btt.name,btt.monthname
order by "supply per capacity " DESC
*/


--How much water does "Neighbourhood 1" receives from "Jane Doe Treatment Plant" in March 2023?--
/*
Select Sum(c.ConsumptionLtr) as 'Total Supply', DATENAME(MONTH, c.Date) as 'monthname',f.Name
      from wcp.Consumptions as c
          INNER JOIN wcp.Properties as p on p.PropertySk=c.PropertySk
          INNER JOIN wcp.Facilities as f on f.FacilitySK=c.FacilitySk
         WHERE DATENAME(MONTH, c.Date)= 'March' and f.Name='Jane Doe Treatment Plant' and p.neighbourhood='neighbourhood 1'
          Group by DATENAME(MONTH, c.Date),f.Name
*/
     
 ---Rank the Customers based on their total Water Consumption--

     /*
Select Sum(c.ConsumptionLtr) as 'Total consumption per customer',cd.CustomerID 
      from wcp.Consumptions as c
          INNER JOIN wcp.Customers as cd on cd.CustomerSK=c.CustomerSk
          INNER JOIN wcp.Facilities as f on f.FacilitySK=c.FacilitySk
          Group by cd.CustomerID
          ORDER by 1 DESC;
*/
