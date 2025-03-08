# Supply Chain Data Cleaning & Analysis using MS SQL & Python

## **📌 Overview**

This project focuses on **cleaning and analyzing a Supply Chain dataset** using **MS SQL Server and Python (Pandas)**. The dataset includes information about **orders, products, suppliers, and warehouses**.

---

## **📂 Steps to Follow**

### **1️⃣ Install MS SQL Server & SSMS**

#### **🔹 Install SQL Server**

- Download **SQL Server Developer Edition**: [Download Link](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
- Install using the **Basic Installation** method.

#### **🔹 Install SSMS (SQL Server Management Studio)**

- Download & install SSMS from [here](https://aka.ms/ssmsfullsetup).
- Open SSMS and connect to `localhost`.

---

### **2️⃣ Create Database in MS SQL**

```sql
CREATE DATABASE SupplyChainDB;
USE SupplyChainDB;
```

---

### **3️⃣ Import the Supply Chain Dataset**

#### **🔹 1. Download Dataset**

- Get the dataset from **[Kaggle](https://www.kaggle.com/datasets/amirmotefaker/supply-chain-dataset)**.
- Extract `supply_chain_data.csv`.

#### **🔹 2. Create Table in SQL**

```sql
CREATE TABLE SupplyChainData (
    OrderID INT PRIMARY KEY,
    ProductName NVARCHAR(255),
    Category NVARCHAR(100),
    OrderDate DATE,
    DeliveryDate DATE,
    Quantity INT,
    UnitPrice DECIMAL(10,2),
    TotalCost DECIMAL(10,2),
    Supplier NVARCHAR(255),
    WarehouseLocation NVARCHAR(255)
);
```

#### **🔹 3. Load CSV Data into SQL**

```sql
BULK INSERT SupplyChainData  
FROM 'C:\Path\To\supply_chain_data.csv'  
WITH (FORMAT='CSV', FIRSTROW=2, FIELDTERMINATOR=',', ROWTERMINATOR='\n');
```

---

### **4️⃣ Data Cleaning in SQL**

#### **🔹 1. Check for Missing Values**

```sql
SELECT * FROM SupplyChainData WHERE ProductName IS NULL OR Quantity IS NULL;
```

#### **🔹 2. Remove Duplicates**

```sql
WITH CTE AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY OrderID ORDER BY OrderID) AS RowNum
    FROM SupplyChainData
)
DELETE FROM CTE WHERE RowNum > 1;
```

#### **🔹 3. Identify Incorrect Dates**

```sql
SELECT OrderID, OrderDate, DeliveryDate  
FROM SupplyChainData  
WHERE OrderDate > DeliveryDate;
```

#### **🔹 4. Remove Negative Values**

```sql
SELECT * FROM SupplyChainData WHERE Quantity < 0 OR UnitPrice < 0;
```

---

### **5️⃣ Export Cleaned Data for Python**

```sql
SELECT * FROM SupplyChainData
INTO OUTFILE 'C:\Path\To\Cleaned_SupplyChain.csv'
FIELDS TERMINATED BY ','  
LINES TERMINATED BY '\n';
```

---

### **6️⃣ Data Cleaning in Python (Pandas)**

#### **🔹 1. Load Data**

```python
import pandas as pd
df = pd.read_csv("C:/Path/To/Cleaned_SupplyChain.csv")
```

#### **🔹 2. Handle Missing Values**

```python
df.fillna({'ProductName': 'Unknown', 'Quantity': df['Quantity'].median()}, inplace=True)
```

#### **🔹 3. Remove Duplicates**

```python
df.drop_duplicates(subset=['OrderID'], keep='first', inplace=True)
```

#### **🔹 4. Fix Incorrect Dates**

```python
df['OrderDate'] = pd.to_datetime(df['OrderDate'], errors='coerce')
df['DeliveryDate'] = pd.to_datetime(df['DeliveryDate'], errors='coerce')
df.loc[df['OrderDate'] > df['DeliveryDate'], ['OrderDate', 'DeliveryDate']] = \
    df.loc[df['OrderDate'] > df['DeliveryDate'], ['DeliveryDate', 'OrderDate']].values
```

#### **🔹 5. Save Cleaned Data**

```python
df.to_csv("C:/Path/To/Final_Cleaned_Data.csv", index=False)
```

---

### **7️⃣ Load Cleaned Data into MS SQL**

```sql
BULK INSERT SupplyChainData
FROM 'C:\Path\To\Final_Cleaned_Data.csv'
WITH (FORMAT='CSV', FIRSTROW=2, FIELDTERMINATOR=',', ROWTERMINATOR='\n');
```

---

### **8️⃣ Data Analysis in SQL**

#### **🔹 1. Top 5 Best-Selling Products**

```sql
SELECT ProductName, SUM(Quantity) AS TotalSold  
FROM SupplyChainData  
GROUP BY ProductName  
ORDER BY TotalSold DESC  
LIMIT 5;
```

#### **🔹 2. Most Delayed Orders**

```sql
SELECT OrderID, DATEDIFF(DAY, OrderDate, DeliveryDate) AS DelayDays  
FROM SupplyChainData  
ORDER BY DelayDays DESC  
LIMIT 5;
```

#### **🔹 3. Total Revenue Per Category**

```sql
SELECT Category, SUM(TotalCost) AS TotalRevenue  
FROM SupplyChainData  
GROUP BY Category  
ORDER BY TotalRevenue DESC;
```

---

## **🚀 Summary**

✅ **Step 1:** Install MS SQL Server & SSMS.\
✅ **Step 2:** Create a database in MS SQL.\
✅ **Step 3:** Import the dataset using **BULK INSERT** or Import Wizard.\
✅ **Step 4:** Clean the data using SQL (remove duplicates, fix dates, handle NULLs).\
✅ **Step 5:** Export cleaned data for **Python processing**.\
✅ **Step 6:** Use **Pandas** for advanced data cleaning.\
✅ **Step 7:** Reload cleaned data into MS SQL.\
✅ **Step 8:** Perform **data analysis** using SQL queries.

---

## **🔗 Resources**

- **Kaggle Dataset**: [Supply Chain Dataset](https://www.kaggle.com/datasets/amirmotefaker/supply-chain-dataset)
- **SQL Server Download**: [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
- **SSMS Download**: [SQL Server Management Studio](https://aka.ms/ssmsfullsetup)

---

## **👨‍💻 Author**

- **Your Name** (Update this section with your details)

---

## **📜 License**

This project is open-source and available under the **MIT License**. Feel free to use and modify it as needed! 🚀

