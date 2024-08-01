PROJECT-Credit card financial dashboard using Power Bi
1. Developed an interactive dashboard using transaction and customer data from a SQL database, to provide real-time insights. 
2. Streamlined data processing & analysis to monitor key performance metrics and trends.
3. Shared actionable insights with stakeholders based on dashboard findings to support decision-making processes.


Step1:- Create the database by using 
Create a database 
CREATE DATABASE ccdb;

step2:- Create the table by using 
1. Create cc_detail table

CREATE TABLE cc_detail (
    Client_Num INT,
    Card_Category VARCHAR(20),
    Annual_Fees INT,
    Activation_30_Days INT,
    Customer_Acq_Cost INT,
    Week_Start_Date DATE,
    Week_Num VARCHAR(20),
    Qtr VARCHAR(10),
    current_year INT,
    Credit_Limit DECIMAL(10,2),
    Total_Revolving_Bal INT,
    Total_Trans_Amt INT,
    Total_Trans_Ct INT,
    Avg_Utilization_Ratio DECIMAL(10,3),
    Use_Chip VARCHAR(10),
    Exp_Type VARCHAR(50),
    Interest_Earned DECIMAL(10,3),
    Delinquent_Acc VARCHAR(5)
);


2. Create cc_detail table

CREATE TABLE cust_detail (
    Client_Num INT,
    Customer_Age INT,
    Gender VARCHAR(5),
    Dependent_Count INT,
    Education_Level VARCHAR(50),
    Marital_Status VARCHAR(20),
    State_cd VARCHAR(50),
    Zipcode VARCHAR(20),
    Car_Owner VARCHAR(5),
    House_Owner VARCHAR(5),
    Personal_Loan VARCHAR(5),
    Contact VARCHAR(50),
    Customer_Job VARCHAR(50),
    Income INT,
    Cust_Satisfaction_Score INT
);

Step3:- Copy csv data into SQL (remember to update the file name and file location in below query)

(a)copy cc_detail table

COPY cc_detail
FROM 'D:\credit_card.csv' 
DELIMITER ',' 
CSV HEADER;


(b)copy cust_detail table

COPY cust_detail
FROM 'D:\customer.csv' 
DELIMITER ',' 
CSV HEADER;

Step4:- Import the data from SQL Postdre to Power Bi


Step5:- These following Dax formulas have been used to make new columns and new measures-y

1. Week_num2 = WEEKNUM('public cc_detail'[week_start_date])

2. wow_Revenue = DIVIDE(([Current_week_Revenue] - [Previous_week_Revenue]),[Previous_week_Revenue])


3. Revenue = 'public cc_detail'[annual_fees] + 'public cc_detail'[total_trans_amt] + 'public cc_detail'[interest_earned]


4. AgeGroup = SWITCH(
    TRUE(),
    'public cust_detail'[customer_age] < 30, "20-30",  
    'public cust_detail'[customer_age] >= 30 && 'public cust_detail'[customer_age]<40, "30-40",
    'public cust_detail'[customer_age] >= 40 && 'public cust_detail'[customer_age]<50, "40-50",
    'public cust_detail'[customer_age] >= 50 && 'public cust_detail'[customer_age]<60, "50-60",
    'public cust_detail'[customer_age] >=60, "60+",
    "unknown"
    )


5. IncomeGroup = SWITCH(
    TRUE(),
    'public cust_detail'[income] < 35000, "Low",  
    'public cust_detail'[income] >= 35000 && 'public cust_detail'[income]<70000, "Med",
    'public cust_detail'[income] >= 70000, "High",
    "unknown"
    )


6. New measure-1

Current_week_Revenue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[Week_num2]=MAX('public cc_detail'[Week_num2])))






7. New measure-2

Previous_week_Revenue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[Week_num2]=MAX('public cc_detail'[Week_num2])-1))



8. COPY cc_detail
FROM 'D:\cc_add.csv' 
DELIMITER ',' 
CSV HEADER;

9. COPY cust_detail
FROM 'D:\cust_add.csv' 
DELIMITER ',' 
CSV HEADER;

SELECT * FROM cust_detail
SELECT * FROM cc_detail


PROJECT INSIGHTS- WEEK 53 (31st Dec 2023)
WOW CHANGE:-
1. Revenue increased by 28.8%.
2. Total transaction Amt & Count increased by 42.54%.
3. Customer count increased by 12K.

Overview YTD:-
1. Overall revenue is 57M.  
2. Total interest is 8M. 
3. Total transaction amount is 46M. 
4. Male customers are contributing more in revenue 31M, female 26M. 
5. Blue & Silver credit card are contributing to 93% of overall transactions.
6. TX, NY & CA is contributing to 68%.
7. Overall Activation rate is 57.5%.
8. Overall Delinquent rate is 6.06%.
