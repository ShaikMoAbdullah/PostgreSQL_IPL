# IPL Dataset Analytics using PostgreSQL
## Index
1. Instructions
2. Introduction
3. About Dataset
4. Objectives
5. References

## 1. Instruction
1. Download the dataset given below
   1. [matches.csv](https://docs.google.com/spreadsheets/d/1h07UsOmHX_aYeGLNvkrym6P3JGckM1YhqbZ3YLdbwtI/edit?usp=sharing)
   2. [deliveries.csv](https://docs.google.com/spreadsheets/d/1LaoA253dYh8pEfwl-EE5MSv-CI4Mj89A6N2XRV-c-Qs/edit?usp=sharing)
   3. [umpires_location.csv](https://docs.google.com/spreadsheets/d/1qhaa0Kwzi6wpY74jCYIgilOSAfHKRPGsuyXtUleyw4Y/edit?usp=sharing)

2. Read the description of the dataset before proceeding with the execution.
3. Install PostgreSQL
   1. We refresh the server's local package index.
    ```
    $ sudo apt update
    ```
    Then, We install the Postgres package along with a -contrib package that adds some additional utilities and functionality:
    ```
    $ sudo apt install postgresql postgresql-contrib
    ```

## 2. Introduction
The Indian Premier League (IPL) is a **professional Twenty20 cricket league**. The league was founded by the **Board of Control for Cricket in India (BCCI) in 2007**. It is usually held between **March and May** of every year.

- The **IPL** is the **most-attended cricket league in the world** and in 2014 was ranked **sixth** by average attendance among all sports leagues. 
- In **2010**, the IPL became the first sporting event in the world to be **broadcasted live** on **YouTube**. 
- The **brand value** of the IPL in **2019** was **₹475 billion (US$6.7 billion), *according to Duff & Phelps*. 
- According to **BCCI**, the 2015 IPL season contributed **₹11.5 billion (US$160 million) to the GDP of the Indian economy**. 
- IPL 2020 set a Massive Viewership Record With **31.57 Million Average Impressions** and with an **overall consumption increase of 23 per cent from 2019 season**.

IPL brings great amount of enthusiasm, energy in the audience and has slowly conected to audience emotions. Therefore, there could be no more reasons to not have an IPL data analysis to better enjoy the IPL season with the history records of the dataset to refresh IPL season back into our memories.

## 3. About dataset
We have considered an IPL dataset from [Kaggle](https://www.kaggle.com/manasgarg/ipl/version/5).
- matches.csv: This file displays the total number of matches playes by the IPL teams for all seasons.
- deliveries.csv: This file displays the number of balls placed in all the matches between all teams in all seasons.
- umpires_location.csv: This file displays the international countries in which IPL umpires belong to.

## 4. Objectives
This project mainly aims to solve 4 main problems.

1. Create **Database**.
2. **Copy** data from **csv** files.
3. To find **Total runs scored by team**.
4. To find **Top batsman for Royal Challengers Bangalore**.
5. To do **Foreign umpire analysis**.
6. To find **Matches played by team by season**

Let's get going with the solutions now.

<br />

### 1. Create Database.
We are creating table column headers for the table **matches**.
```
CREATE TABLE matches (id integer,
  season integer,
  city varchar,
  date date,
  team1 varchar, 
  team2 varchar, 
  toss_winner varchar, 
  toss_decision varchar, 
  result varchar, 
  dl_applied integer, 
  winner varchar, 
  win_by_runs integer, 
  win_by_wickets integer, 
  player_of_the_match varchar, 
  venue varchar, 
  umpire1 varchar, 
  umpire2 varchar, 
  umpire3 integer
);
```
Next, We are creating table column headers for the table **deliveries**.
```
CREATE TABLE deliveries (
  matchid integer, 
  inning integer, 
  batting_team varchar, 
  bowling_team varchar, 
  over integer, 
  ball integer, 
  batsman varchar, 
  non_striker varchar, 
  bowler varchar, 
  is_super_over integer, 
  wide_runs integer, 
  bye_runs integer, 
  legbye_runs integer, 
  noball_runs integer, 
  penalty_runs integer, 
  batsman_runs integer, 
  extra_runs integer, 
  total_runs integer, 
  player_dismissed varchar, 
  dismissal_kind varchar, 
  fielder varchar
);
```
### 2. Copy data from csv files
We have copied the rows for the deliveries table.
```
ipl=# copy deliveries from '/home/shaik/Documents/Mountblue/Serverside development/Python/Projects/Project_1/ipl_project/deliveries.csv' with (format 'csv', header true);
```
You get the output as:
```
COPY 150460
```
This tells you the operation did and the number of rows copied. In this case, We have got **150460 rows** copied.

Next, We have copied the rows for the matches table.
```
ipl=# copy matches from '/home/shaik/Documents/Mountblue/Serverside development/Python/Projects/Project_1/ipl_project/matches.csv' with (format 'csv', header true);
```
You get the output as:
```
COPY 636
```
This tells you the operation did and the number of rows copied. In this case, We have got **636 rows** copied.

### 3. Total runs scored by the team 

We observe that we have the complete details of all the balls in an innings in the "deliveries.csv" file. We map the batting team  with the actual scores per ball.

The query is:
```
SELECT batting_team, SUM(total_runs) FROM deliveries GROUP BY batting_team ORDER BY sum desc;
```

We get the output as follows.
```
        batting_team         |  sum  
-----------------------------+-------
 Mumbai Indians              | 24521
 Royal Challengers Bangalore | 23436
 Kings XI Punjab             | 23068
 Kolkata Knight Riders       | 21965
 Delhi Daredevils            | 21953
 Chennai Super Kings         | 20899
 Rajasthan Royals            | 17703
 Sunrisers Hyderabad         | 11652
 Deccan Chargers             | 11463
 Pune Warriors               |  6358
 Gujarat Lions               |  4862
 Rising Pune Supergiant      |  2470
 Rising Pune Supergiants     |  2063
 Kochi Tuskers Kerala        |  1901
(14 rows)
```
This above snippet does not display the complete output. This is just to show the pattern of the output looks like.

Through this analysis, We come to know the **total runs** scored by each team.

### 4. Top batsman for Royal Challengers Bangalore
We map between the Royal Challengers Bangalore batsman and the total scores gained by him.

The query is:
```
SELECT batsman, SUM(total_runs) FROM deliveries WHERE batting_team = 'Royal Challengers Bangalore' GROUP BY batsman ORDER BY sum desc;
```
We get the output as follows.
```
     batsman      | sum  
------------------+------
 V Kohli          | 9176
 CH Gayle         | 6778
 AB de Villiers   | 5866
 JH Kallis        | 2428
 R Dravid         | 1848
 TM Dilshan       | 1292
 RV Uthappa       | 1158
 LRPL Taylor      | 1092
 SS Tiwary        | 1048
 MA Agarwal       |  926
 MK Pandey        |  872
 KL Rahul         |  870
 MV Boucher       |  812
 Yuvraj Singh     |  794
 Mandeep Singh    |  712
 KP Pietersen     |  690
 KM Jadhav        |  654
 R Vinay Kumar    |  518
 SR Watson        |  510
 PA Patel         |  434
 TM Head          |  428
 P Kumar          |  376
 SN Khan          |  360
 MC Henriques     |  342
 CL White         |  334
 P Negi           |  308
 STR Binny        |  300
 KD Karthik       |  298
 CA Pujara        |  298
 SP Goswami       |  296
:
 ...
```
This above snippet does not display the complete output. This is just to show the pattern of the output looks like.

Through this analysis, We come to know the **Virat Kohli** has the top runs in IPL matches in **Royal Challengers Bangalore**. 

### 5. To do Foreign umpire analysis.
We map between the umpire residing countries with the number of umpires. 

The query is:
```
SELECT DISTINCT * FROM umpire_location;
```
We get the output as follows.
```
   country    
--------------
 Sri Lanka
 Zimbabwe
 Australia
 West Indies
 England
 South Africa
 New Zealand
 Pakistan
(8 rows)
```

Through this analysis, We come to know the **Australian umpires** are more in IPL matches and West Indies, Zimbabwe umpires are less in IPL matches.

### 6. To find Matches played by team by season
We map between the teams with the number of games played per season. 

The query is:
```
SELECT DISTINCT team1, season, COUNT(id) FROM matches GROUP BY team1, season ORDER BY team1, season;
```
We get the output as follows.
```
            team1            | season | count 
-----------------------------+--------+-------
 Chennai Super Kings         |   2008 |     9
 Chennai Super Kings         |   2009 |    10
 Chennai Super Kings         |   2010 |     8
 Chennai Super Kings         |   2011 |    12
 Chennai Super Kings         |   2012 |    12
 Chennai Super Kings         |   2013 |     8
 Chennai Super Kings         |   2014 |     7
 Chennai Super Kings         |   2015 |    11
 Deccan Chargers             |   2008 |     8
 Deccan Chargers             |   2009 |     8
 Deccan Chargers             |   2010 |     7
 Deccan Chargers             |   2011 |     9
 Deccan Chargers             |   2012 |    11
 Delhi Daredevils            |   2008 |     6
 Delhi Daredevils            |   2009 |     7
 Delhi Daredevils            |   2010 |     6
 Delhi Daredevils            |   2011 |     7
 Delhi Daredevils            |   2012 |     4
 Delhi Daredevils            |   2013 |     7
 Delhi Daredevils            |   2014 |     7
 Delhi Daredevils            |   2015 |     7
 Delhi Daredevils            |   2016 |     6
 Delhi Daredevils            |   2017 |     6
 Gujarat Lions               |   2016 |     6
 Gujarat Lions               |   2017 |     8
 Kings XI Punjab             |   2008 |     7
 Kings XI Punjab             |   2009 |     6
:
```
This above snippet does not display the complete output. This is just to show the pattern of the output looks like.

Through this analysis, We can see the complete information of the number of matches played per team per season.

# References
- [How to install PostgreSQL on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart)
- [Interoduction to SQL](https://sqlbolt.com/)
- [Kaggle dataset](https://www.kaggle.com/manasgarg/ipl/version/5)