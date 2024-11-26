## Project Overview
The analysis has the following objectives:
1. Uncovering Trends: Detect and visualize trends in complaint volume and types over the years.
2. Evaluating Response Effectiveness: Assess the timeliness and adequacy of BoA’s responses to complaints.
3. Identifying Recurring Issues: Highlight common problems faced by consumers and evaluate BoA’s effectiveness in addressing these issues.

## Expected outcomes
+ Provide actionable recommendations for improving customer service and operational practices based on the findings.
+ Include the limitations of the dataset and how this analysis can be further enriched

## Tools Used
To complete the study, the following data analytics tools were utilized:

1. Excel - For data import and cleaning
2. SQL - For more cleaning and analysis
3. Power BI - For visualization

## Content of the dataset
The following columns are contained in the dataset:
+ Complaint ID
+ Submitted via
+ Date submitted
+ Date received
+ State
+ Product
+ Sub-product
+ Issue
+ Sub-issue
+ Company public response
+ Company response to consumer
+ Timely response?

## Methods and Questions
To begin the analysis, I first cleaned up the table by identifying blanks and nulls and filling them up if possible. Also, I normalized dates and formatted columns to enable a seamless analysis.

Some questions answered include:
1. What is the percentage of the company's responses to consumer complaints?
2. What are the total number of issues by state?
3. What is the trend of complaints across the years?
4. What are the percentages of complaint media used?
5. What is the volume of complaints by product type?
6. What are the most reocurring issues and subissues?


## Code
--Sql

-------------------------------------------Duplicate table---------------------------------------------------------------------------------

CREATE TABLE cc LIKE consumers;
INSERT INTO cc SELECT * FROM consumers;


SELECT * FROM consumer_complaints.consumers;

ALTER TABLE consumer_complaints RENAME TO consumers;

---------------Data Cleaning (Check for blank rows in the columns; sub-issue, company public response, and timely response)-----------------

UPDATE consumers
SET `Sub-issue` = 'UNKNOWN'
WHERE TRIM(`Sub-issue`) = '' OR `Sub-issue` IS NULL;

---------------------------Decided to change unknown to undisclosed---------------------------------------------------------------------

UPDATE consumers
SET `Sub-issue` = 'Undisclosed'
WHERE `Sub-issue` = 'UNKNOWN';

----------------------------------Name blanks in column, sub-product-------------------------------------------------------------------------

UPDATE consumers
SET `Company public response` = 'None given yet'
WHERE `Company public response` = 'Undisclosed';

-------------------------------Name blanks in column, timely response-----------------------------------------------------------------------

UPDATE consumers
SET `Timely response?` = 'Not determined'
WHERE TRIM(`Timely response?`) = '' OR `Timely response?` IS NULL;


-------------------Proceed to analysis having handled nulls--------------------------------------------------------------------------------

SELECT 
    COUNT(DISTINCT state) AS distinct_state_count
FROM 
    consumer_complaints.consumers;
    
------------------Give states their real names-----------------------------------------------------------------------------------------

UPDATE consumers
SET State =
CASE
    WHEN State = 'AK' THEN 'Alaska'
    WHEN State = 'AL' THEN 'Alabama'
    WHEN State = 'AR' THEN 'Arkansas'
    WHEN State = 'AZ' THEN 'Arizona'
    WHEN State = 'CA' THEN 'California'
    WHEN State = 'CO' THEN 'Colorado'
    WHEN State = 'CT' THEN 'Connecticut'
    WHEN State = 'DC' THEN 'District of Columbia'
    WHEN State = 'DE' THEN 'Delaware'
    WHEN State = 'FL' THEN 'Florida'
    WHEN State = 'GA' THEN 'Georgia'
    WHEN State = 'HI' THEN 'Hawaii'
    WHEN State = 'IA' THEN 'Iowa'
    WHEN State = 'ID' THEN 'Idaho'
    WHEN State = 'IL' THEN 'Illinois'
    WHEN State = 'IN' THEN 'Indiana'
    WHEN State = 'KS' THEN 'Kansas'
    WHEN State = 'KY' THEN 'Kentucky'
    WHEN State = 'LA' THEN 'Louisiana'
    WHEN State = 'MA' THEN 'Massachusetts'
    WHEN State = 'MD' THEN 'Maryland'
    WHEN State = 'ME' THEN 'Maine'
    WHEN State = 'MI' THEN 'Michigan'
    WHEN State = 'MN' THEN 'Minnesota'
    WHEN State = 'MO' THEN 'Missouri'
    WHEN State = 'MS' THEN 'Mississippi'
    WHEN State = 'MT' THEN 'Montana'
    WHEN State = 'NC' THEN 'North Carolina'
    WHEN State = 'ND' THEN 'North Dakota'
    WHEN State = 'NE' THEN 'Nebraska'
    WHEN State = 'NH' THEN 'New Hampshire'
    WHEN State = 'NJ' THEN 'New Jersey'
    WHEN State = 'NM' THEN 'New Mexico'
    WHEN State = 'NV' THEN 'Nevada'
    WHEN State = 'NY' THEN 'New York'
    WHEN State = 'OH' THEN 'Ohio'
    WHEN State = 'OK' THEN 'Oklahoma'
    WHEN State = 'OR' THEN 'Oregon'
    WHEN State = 'PA' THEN 'Pennsylvania'
    WHEN State = 'RI' THEN 'Rhode Island'
    WHEN State = 'SC' THEN 'South Carolina'
    WHEN State = 'SD' THEN 'South Dakota'
    WHEN State = 'TN' THEN 'Tennessee'
    WHEN State = 'TX' THEN 'Texas'
    WHEN State = 'UT' THEN 'Utah'
    WHEN State = 'VA' THEN 'Virginia'
    WHEN State = 'VT' THEN 'Vermont'
    WHEN State = 'WA' THEN 'Washington'
    WHEN State = 'WI' THEN 'Wisconsin'
    WHEN State = 'WV' THEN 'West Virginia'
    WHEN State = 'WY' THEN 'Wyoming'
    ELSE 'Unknown State'
END;
    

---------------------Complaint by state----------------------------------------------------------------------------------------------------
SELECT
State,
COUNT(Issue) AS Number_of_issues
FROM  consumer_complaints.consumers
GROUP BY State
ORDER BY Number_of_issues DESC;

-------------------Complaint medium used-------------------------------------------------------------------------------------------------

SELECT
`Submitted via` AS complaint_medium,
ROUND(count(`Submitted via`) * 100.0 / sum(count(`Submitted via`)) over(), 2) AS percentage
FROM  consumer_complaints.consumers
GROUP BY complaint_medium;

------------------------Products by issues------------------------------------------------------------------------------------------------

SELECT 
Product,
COUNT(Issue) AS Issue_count
FROM  consumer_complaints.consumers
GROUP BY 1
ORDER BY 2 DESC;

------------------------Complaint count across years--------------------------------------------------------------------------------------

SELECT 
COUNT(Issue) AS number_of_issues,
YEAR(`Date submitted`) AS years
FROM consumer_complaints.consumers
GROUP BY 2
ORDER BY 1 DESC;

----------------------------Response rates of companies to consumers------------------------------------------------------------------------

SELECT 
`Company response to consumer` AS responses,
ROUND(count(`Company response to consumer`) * 100.0 / sum(count(`Company response to consumer`)) over(), 2) AS rate_of_response
FROM consumer_complaints.consumers
GROUP BY 1;

-------------------------Most occurring issues and subissues--------------------------------------------------------------------------

SELECT 
Issue,
ROUND(COUNT(`Sub-issue`), -3) AS Number_of_sub_issues
FROM consumer_complaints.consumers
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;

-----------------Issues by company response to consumers------------------------------------------------------------------------------

SELECT
Issue,
COUNT(`Company response to consumer`) AS responses
FROM consumer_complaints.consumers
GROUP BY 1;

--sql
