# SQL-Water-Corp-Project
SQL and Data Visualization Hands-On using Water Corporation Dataset (Water Consumption)
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

   
