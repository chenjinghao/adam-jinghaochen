# Capstone Project of Google Data Analytics Professional Certificate

## About.

This is my first online course. I have done much research comparing data analytic certification from Google and IBM. Ultimately, I picked the google certification because the generated feedbacks were primarily positive.

- Learning
    - The program gave me a general overview of a data analyst's daily life, as well as introduced tools and resources to gain more insight.
    - Power BI runs faster than the Tableau community version, in my case.
    - Power BI can do most of the job. However, the data set we were provided was relatively small. So Power BI or other BI applications can handle the workload. In the real world, it will be a different story. Hence, mastering programming languages is essential for data analysis.
    - SQLite has a limited number of functions, which require deep understanding to achieve tasks that caquicklyly be done in other SQL. The example has shown below.
    
    ```sql
    -- To calculate time different
    -- SQLite database
    SELECT
    	ride_id,
    	round((julianday(ended_at) - julianday(started_at)) * 1440) as ride_length
    FROM
    	all_divvy_tripdata
    
    -- Microsoft SQL server
    SELECT 
    	[ride_id],
    	[ended_at],
    	[started_at],
    	DATEDIFF(MINUTE,[started_at],[ended_at]) AS [ride_length]
    FROM 
    	[dbo].all_trip_data_here
    ```
    
- Limitation and future
    
    Many tools are introduced, such as r programming language, Tableau,  SQL etc. Since this certification only went through the very basics of these tools. I felt that is not enough. Hence, I took extra courses to enhance my skill. So far, I have taken and passed PowerBI PL-300 Exam, Excel for Business. I also have an ongoing course, “SQL for data science.” After this course, I will take a “Data Analysis with Python” course from freecodecamp.org.
    

Since my PowerBI trail is expired, I have exported the dashboard below. 

[GDAC_capstone_project_dashboard.pptx](GDAC_capstone_project_dashboard.pptx)

[GDAC_capstone_project_dashboard_weekend.pptx](GDAC_capstone_project_dashboard_weekend.pptx)

---

### Capstone Project - **Google Data Analytics Professional Certificate**

**Cyclic bike-share analysis case study**

**Introduction** 

I have chosen a cyclitic bike-share case study project as my capstone project. When I visited the website to download the dataset, I realized it was constantly updating, making it a real-world dataset. This means I can act as a junior data analyst and provide insight to my supervisor. 

The case study will follow google’s recommended roadmap shown in the following:

<aside>
✅ A**sk → Prepare → Process → Analyze → Share → Act**

</aside>

**Scenario** 

At this moment, the company offers three pricing plans - single-ride passes, full-day passes, and annual memberships - to customers who are either casual riders or Cyclists members. Furthermore, an annual membership is more profitable compared to a casual rider.

Lily Moreno, the marketing director and your manager, has set a clear goal: Design marketing strategies to convert casual riders into annual members. She understands that most casual riders are aware of the membership program. The company stands a good chance of converting them into annual members with fewer resources. It is believed that maximizing annual members will be critical to future growth.

The team must present compelling insights and professional data visualizations to the company's executives to gain approval for their new marketing strategy.

---

### 1. ASK

**Business Task:** 

Moreno has assigned you the first question to answer: 

<aside>
❓ How do annual members and casual riders use Cyclistic bikes differently?

</aside>

**Key Stakeholders:**

- Lily Moreno, the director of marketing
- The marketing analyst team

### 2. Prepare

**Data Source**

Download data from [here](https://divvy-tripdata.s3.amazonaws.com/index.html)

- Selected period: Dec 2021 - Nov 2022

All data is provided under this license [here](https://ride.divvybikes.com/data-license-agreement).

**Data Structure**

There are 12 .csv files downloaded from the address mentioned above. Each .csv file contains 13 columns and hundreds of thousands of rows. 

**Limitation**

- There are null values in almost every column except the ride_id column. Most are in columns containing geographic information, location name and location id.
- The dataset does not provide any rider’s information such as age, income level, employment, orders etc. This prohibited the data analyst from investigating further to understand the riders' behaviour.

### 3. Process

**Tools**

- DB Browser for SQLite
- Microsoft Power Bi Desktop

**Reasons:** 

- DB Browser for SQLite: It is a lightweight database management software, which I believe is suitable for this workload size. Especially the data are all stored on local hard disks, which makes me easy to set up a database in the DB Browser.
- Microsoft Power Bi Desktop: I have downloaded Tableau and Power BI to compare. However, Tableau is relatively slower than Power BI when transforming data. I am also more familiar with Power BI since I have passed Microsoft PL-300 Microsoft Power BI Data Analyst certification.

**Steps**

- DB Browser for SQLite
    1. Import all .csv files
        - Steps
            1. Create “New Database” → name your database → “save”
            2. “File” → “Import” → “Table from CSV file…” → select all CSV files → select “Separate tables“ → “OK”
            3. Press “Brower Data” to view the data structure 
            4. Select “Execute SQL” or open my SQL file
    2. View data - use the “Browse Data” tag. 
    3. Combine all .csv files into **ONE** name `all_divvy_tripdata` 
    4. checking duplicate/null/inconsistent primary key (`ride_id`)
    5. understand `rideable_type` column
        - How many types of bike
        - How many times has used each type of bike
            
            <aside>
            💡 classic_bike	2628499
            docked_bike	180477
            electric_bike	2924475
            
            </aside>
            
        - delete docked_bike from rideable_id column
            
            It only takes a small portion (3.25%) of the total data. Deleting the `docked_bike` from `rideable_id` will not change the result.
            
            ![Untitled](Untitled%201.png)
            
    6. add `ride_lenght` into the table
        1. add `ride_lenght` column into the table
        2. calculate the length of each ride
        3. update the `ride_length` in the main table
        4. delete `ride_length` ≤ 1, and renting a bike for less than a minute is unusual. 
    7. add `hour_of_day` into the table
        1. convert and check the strftime function
        2. create an `hour_of_day` column in the table
        3. update `hour_of_day` column
    8. find favour route
    9. export table as CSV. file
        1. select table inside the ‘Database Structure’ tag
        2. right-click and select ‘Export as CSV file.’
    - **View all SQL code here**
        
        ```sql
        -- 3. combine all .csv files into one
        CREATE TABLE 'all_divvy_tripdata' AS 
        SELECT * FROM '202112-divvy-tripdata' 
        UNION ALL
        SELECT * FROM '202201-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202202-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202203-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202204-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202205-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202206-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202207-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202208-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202209-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202210-divvy-tripdata'
        UNION ALL
        SELECT * FROM '202211-divvy-tripdata'
        
        -- 4. checking duplicate/null/inconsistent primary key (ride_id)
        -- Find Null value
        SELECT
        	ride_id
        FROM
        	all_divvy_tripdata
        WHERE
        	ride_id is NULL;
        -- Result: 0 rows returned in 4661ms
        
        -- Find duplicate value
        SELECT
        	count(ride_id)
        FROM
        	all_divvy_tripdata
        GROUP by
        	ride_id
        HAVING
        	count(ride_id) > 1;
        -- Result: 0 rows returned in 22585ms
        
        -- check length of ride_id no more than 16 characters
        SELECT
        	ride_id
        FROM
        	all_divvy_tripdata
        WHERE
        	length(ride_id)>16;
        -- Result: 0 rows returned in 4961ms
        
        -- 5. understand rideable_type column
        -- found the distribution of rideable_type
        SELECT
        	rideable_type,
        	count(rideable_type) AS no_of_time_use
        FROM
        	all_divvy_tripdata
        GROUP by
        	rideable_type;
        
        -- delete docked_bike from rideable_id column
        DELETE FROM
        	all_divvy_tripdata
        WHERE
        	rideable_type = 'docked_bike';
        -- Result: query executed successfully. Took 3071ms, 180477 rows affected
        
        -- 6. Add ride_lenght into table
        -- a. Add ride_length into the table
        ALTER TABLE all_divvy_tripdata ADD COLUMN ride_length INTEGER;
        
        -- b. calculate & check the ride length
        SELECT
        	ride_id,
        	round((julianday(ended_at) - julianday(started_at)) * 1440) as ride_length
        FROM
        	all_divvy_tripdata
        
        -- c. update all_divvy_tripdata table with ride_length
        UPDATE 
        	all_divvy_tripdata
        SET
        	ride_length = round(1440*(julianday(ended_at) - julianday(started_at)));
        
        -- d. delete ride_length ≤ 1
        DELETE FROM 
        	all_divvy_tripdata
        WHERE
        	ride_length <= 1
        -- Result: query executed successfully. Took 1554ms, 159335 rows affected
        
        -- 7. Add hour_of_day into table
        -- 1. convert and check strftime function
        SELECT
        	ride_id,
        	strftime('%H',started_at) as hour_of_day
        FROM
        	all_divvy_tripdata;
        -- 2. create hour_of_day column into table
        ALTER TABLE all_divvy_tripdata ADD COLUMN hour_of_day INTEGER;
        -- 3. update hour_of_day column
        UPDATE
        	all_divvy_tripdata
        SET hour_of_day = strftime('%H',started_at);
        -- Result: query executed successfully. Took 8612ms, 5393639 rows affected
        
        -- 8. Find favour routes
        -- 1. check the query
        SELECT
        	start_station_name || ' --- ' || end_station_name as route
        FROM
        	all_divvy_tripdata;
        -- 2. add new route column 
        ALTER TABLE all_divvy_tripdata ADD COLUMN route TEXT;
        -- 3. update route into main table
        UPDATE 
        	all_divvy_tripdata
        SET route = start_station_name || ' --- ' || end_station_name;
        --Result: query executed successfully. Took 14983ms, 5393639 rows affected
        ```
        
- Power BI
    1. Import `all_divvy_tripdata`.csv file into Power BI
    2. Save
    3. Enter data mode and use DAX to create `day_of_week` and `weekday` columns
        1. day_of_week = FORMAT([started_at],"dddd")
        2. weekday = WEEKDAY([started_at],2)
    4. use ‘column tools’ →  select `day_of_week` column→ ‘Sort by column’ → pick `weekday` column
    

### 4. Analysis

**Overview - The Power BI dashboard**

![Untitled](Untitled%202.png)

HD version: 

[GDAC_capstone_project_dashboard.pptx](GDAC_capstone_project_dashboard%201.pptx)

Key points: 

Users pit chart

- Annual members account for more than 60% of total rides during these 12 months.
    
    Number of rides by weekday and user type
    
- Causal riders demand more bikes during the weekend. At the same time, the gap is getting very close between casual and member riders.
    
    Number of rides by hours
    
- Annual members are riding more bikes during peak hours of the day. (8 am & 5 pm)
    
    Number of rides by Month
    
- The demand for bikes for Causal riders peaked in July, and the gap between the two types of riders is getting narrowed down.
    
    Average ride length
    
- Casual riders preferred to ride the `classic_bike` as the average ride length of the classic bike is significantly higher than the `electric_bike`.

**Weekend**

![Untitled](Untitled%203.png)

HD version: 

[GDAC_capstone_project_dashboard_weekend.pptx](GDAC_capstone_project_dashboard_weekend%201.pptx)

Key points:

Number of rides by weekday and user type

- During the weekend, the number of rides taken by casual riders is almost the same as an annual member. This can be observed from the charts.
    
    Number of rides by hours
    
- Causal riders are more active than members during a specific period. For instant, casual riders use the service more than members between 14:00 - 16:00.
    
    Number of rides by month
    
- Also, the number of casual riders surpassed annual members in May, reaching the peak before August and falling below the annual member after September. This suggests that more travellers visit Chicago during this period.

### 5. Share

I am sharing my pdf file export from Power BI. My Power BI free trial will end soon, and I am not sure the viewers will have access later. 

[GDAC_capstone_project_dashboard.pdf](GDAC_capstone_project_dashboard.pdf)

### 6. Act

We have built a dashboard to visualize the data to answer the business task, “How do annual members and casual riders use Cyclistic bikes differently?”. It clearly shows that. 

1. Casual riders demand the share bike service more during the weekend. 
2. The number of Casual riders’ rides surpass member riders on weekends between May and September. 
3. Casual riders preferred to ride classic bikes over electric bikes.
4. During the day, member riders use the service intensively during rush hours.  
5. The No.1 favour route change during the weekend. 

**Recommendation**

1. The company should offer **weekend-only annual passes** to target customers who only need share bike service during the weekend. Since the top no.1 favour route change during the weekend, the company should consider making more bikes available. Hence more casual riders can explore our bike and attract them to membership.  
2. Providing a monthly pass to catch those travellers between May and September. The monthly pass should come with limitations that limit the ride length of the new member. The marketing campaign should also consider launching at this period to achieve maximum return.  
3. Since casual riders prefer to ride a classic bike, the company also can consider providing **classic bikes only for the annual membership**. 
4. For the above two recommendations, the company should also consider adding restrictions such as extra cost for rush hours during the workday.
5. The company should discuss the potential to provide incentives for existing customers to promote the service. Word of mouth is the best sale pitch. 
6. The company can consider targeting casual riders who have used the service more than ten times and offer promotional prices just for them. This group of users indicated they needed the service but not as frequently as the annual members. 

**the END**

---