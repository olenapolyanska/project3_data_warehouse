
### Data Engineering Nanodegree Program

## PROJECT 3: DATA WAREHOUSE

#### INTRODUCTION

The Spyrkify's startup analytics team collected information about user activity and music listened from a new muic streaming application. The amount of received information makes it reasonable to process it on the cloud.
So idea is to build an ETL pipeline that extracts their data from S3, stages them in Redshift, and transforms data into a set of dimensional tables with the purpose to optimize and simplify analytics work.

Also the tasks that assigned to the data engeneer are : to test created database and ETL pipeline by running queries given to him by the analytics team from Sparkify and compare the results with their expected results.

---

#### PROJECT STEPS

To complete this project the data engeneer have to do next:

1. Create Table Schemas
- Design schemas for fact and dimension tables
- Write a SQL CREATE statement for each of these tables in sql_queries.py
- Complete the logic in create_tables.py to connect to the database and create these tables
- Write SQL DROP statements to drop tables in the beginning of create_tables.py if the tables already exist. 

2. Launch a redshift cluster
- Launch a redshift cluster and create an IAM role that has read access to S3.
- Add redshift database and IAM role info to dwh.cfg.
- Test by running create_tables.py and checking the table schemas in the redshift database. 

3. Build ETL Pipeline
- Implement the logic in etl.py to load data from S3 to staging tables on Redshift.
- Implement the logic in etl.py to load data from staging tables to analytics tables on Redshift.
- Test by running etl.py after running create_tables.py and running the analytic queries on the Redshift database to compare your results with the expected results.
- Delete the redshift cluster when finished.

---

#### SCHEMA DESIGN
 
The created by data engineer _star schema design_ includes
one Fact Table and four Dimension Tables.

1. The Fact Table is songplays table.
2. Dimension Tables are users, songs, artists, time tables.

![\"sparkify\" database ERD](project3_erd.PNG)

Using the song and log datasets a star schema optimized for queries on song 
play analysis.

---

#### DATA SET

The complete Data Set to be processed consists of two datasets that reside in S3. 
Here are the S3 links for each:

Song data: s3://udacity-dend/song_data
Log data: s3://udacity-dend/log_data
Log data json path: s3://udacity-dend/log_json_path.json

- Song data
The Song data set is the subset of real data from the Million Song Dataset (https://labrosa.ee.columbia.edu/millionsong/). The song data set contains metadata about a song and the artist of that song stored in the JSON files. The files are partitioned by the first three letters of each song's track ID. For example, here are filepaths to three files in song dataset in s3://udacity-dend bucket:
```
SONG_DATA

song_data/
song_data/A/A/A/TRAAAAK128F9318786.json
song_data/A/A/A/TRAAAAV128F421A322.json
song_data/A/A/A/TRAAABD128F429CF47.json
```

Here is the example of the one song file content (s3://udacity-dend/song_data/A/A/A/TRAAAAK128F9318786.json):

```json
{"artist_id":"ARJNIUY12298900C91","artist_latitude":null,"artist_location":"","artist_longitude":null,"artist_name":"Adelitas Way","duration":213.9424,"num_songs":1,"song_id":"SOBLFFE12AF72AA5BA","title":"Scream","year":2009}
```

- Log data
The Log dataset in the dataset generated by this event simulator based on the Song dataset. The Log dataset information is stored in the JSON format files which are partitioned by year and month. For example, here are filepaths to three files in log dataset in s3://udacity-dend bucket: 
```
LOG_DATA

log_data/
log_data/2018/11/2018-11-01-events.json
log_data/2018/11/2018-11-02-events.json
log_data/2018/11/2018-11-03-events.json

```

Here is the example of the one log file content (data/log_data/2018/11/2018-11-01-events.json):

```json
{"artist":null,"auth":"Logged In","firstName":"Walter","gender":"M","itemInSession":0,"lastName":"Frye","length":null,"level":"free","location":"San Francisco-Oakland-Hayward, CA","method":"GET","page":"Home","registration":1540919166796.0,"sessionId":38,"song":null,"status":200,"ts":1541105830796,"userAgent":"\"Mozilla\/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/36.0.1985.143 Safari\/537.36\"","userId":"39"}
{"artist":null,"auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":0,"lastName":"Summers","length":null,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"GET","page":"Home","registration":1540344794796.0,"sessionId":139,"song":null,"status":200,"ts":1541106106796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"Des'ree","auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":1,"lastName":"Summers","length":246.30812,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"PUT","page":"NextSong","registration":1540344794796.0,"sessionId":139,"song":"You Gotta Be","status":200,"ts":1541106106796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":null,"auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":2,"lastName":"Summers","length":null,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"GET","page":"Upgrade","registration":1540344794796.0,"sessionId":139,"song":null,"status":200,"ts":1541106132796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"Mr Oizo","auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":3,"lastName":"Summers","length":144.03873,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"PUT","page":"NextSong","registration":1540344794796.0,"sessionId":139,"song":"Flat 55","status":200,"ts":1541106352796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"Tamba Trio","auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":4,"lastName":"Summers","length":177.18812,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"PUT","page":"NextSong","registration":1540344794796.0,"sessionId":139,"song":"Quem Quiser Encontrar O Amor","status":200,"ts":1541106496796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"The Mars Volta","auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":5,"lastName":"Summers","length":380.42077,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"PUT","page":"NextSong","registration":1540344794796.0,"sessionId":139,"song":"Eriatarka","status":200,"ts":1541106673796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"Infected Mushroom","auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":6,"lastName":"Summers","length":440.2673,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"PUT","page":"NextSong","registration":1540344794796.0,"sessionId":139,"song":"Becoming Insane","status":200,"ts":1541107053796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"Blue October \/ Imogen Heap","auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":7,"lastName":"Summers","length":241.3971,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"PUT","page":"NextSong","registration":1540344794796.0,"sessionId":139,"song":"Congratulations","status":200,"ts":1541107493796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"Girl Talk","auth":"Logged In","firstName":"Kaylee","gender":"F","itemInSession":8,"lastName":"Summers","length":160.15628,"level":"free","location":"Phoenix-Mesa-Scottsdale, AZ","method":"PUT","page":"NextSong","registration":1540344794796.0,"sessionId":139,"song":"Once again","status":200,"ts":1541107734796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/35.0.1916.153 Safari\/537.36\"","userId":"8"}
{"artist":"Black Eyed Peas","auth":"Logged In","firstName":"Sylvie","gender":"F","itemInSession":0,"lastName":"Cruz","length":214.93506,"level":"free","location":"Washington-Arlington-Alexandria, DC-VA-MD-WV","method":"PUT","page":"NextSong","registration":1540266185796.0,"sessionId":9,"song":"Pump It","status":200,"ts":1541108520796,"userAgent":"\"Mozilla\/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit\/537.77.4 (KHTML, like Gecko) Version\/7.0.5 Safari\/537.77.4\"","userId":"10"}
{"artist":null,"auth":"Logged In","firstName":"Ryan","gender":"M","itemInSession":0,"lastName":"Smith","length":null,"level":"free","location":"San Jose-Sunnyvale-Santa Clara, CA","method":"GET","page":"Home","registration":1541016707796.0,"sessionId":169,"song":null,"status":200,"ts":1541109015796,"userAgent":"\"Mozilla\/5.0 (X11; Linux x86_64) AppleWebKit\/537.36 (KHTML, like Gecko) Ubuntu Chromium\/36.0.1985.125 Chrome\/36.0.1985.125 Safari\/537.36\"","userId":"26"}
{"artist":"Fall Out Boy","auth":"Logged In","firstName":"Ryan","gender":"M","itemInSession":1,"lastName":"Smith","length":200.72444,"level":"free","location":"San Jose-Sunnyvale-Santa Clara, CA","method":"PUT","page":"NextSong","registration":1541016707796.0,"sessionId":169,"song":"Nobody Puts Baby In The Corner","status":200,"ts":1541109125796,"userAgent":"\"Mozilla\/5.0 (X11; Linux x86_64) AppleWebKit\/537.36 (KHTML, like Gecko) Ubuntu Chromium\/36.0.1985.125 Chrome\/36.0.1985.125 Safari\/537.36\"","userId":"26"}
{"artist":"M.I.A.","auth":"Logged In","firstName":"Ryan","gender":"M","itemInSession":2,"lastName":"Smith","length":233.7171,"level":"free","location":"San Jose-Sunnyvale-Santa Clara, CA","method":"PUT","page":"NextSong","registration":1541016707796.0,"sessionId":169,"song":"Mango Pickle Down River (With The Wilcannia Mob)","status":200,"ts":1541109325796,"userAgent":"\"Mozilla\/5.0 (X11; Linux x86_64) AppleWebKit\/537.36 (KHTML, like Gecko) Ubuntu Chromium\/36.0.1985.125 Chrome\/36.0.1985.125 Safari\/537.36\"","userId":"26"}
{"artist":"Survivor","auth":"Logged In","firstName":"Jayden","gender":"M","itemInSession":0,"lastName":"Fox","length":245.36771,"level":"free","location":"New Orleans-Metairie, LA","method":"PUT","page":"NextSong","registration":1541033612796.0,"sessionId":100,"song":"Eye Of The Tiger","status":200,"ts":1541110994796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.3; WOW64) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/36.0.1985.143 Safari\/537.36\"","userId":"101"}
```

---

#### PROJECT ARCHITECTURE

The project software consists of three python files:

1. create_table.py 
This is where the fact and dimension tables for the star schema in Redshift are created.
2. etl.py 
This is where the data loading from S3 into staging tables on Redshift and then processing of that data into analytics tables on Redshift take place.
3. sql_queries.py 
This is where are defined SQL statements, which will be imported into the two other files above.
4. And the project contains README.md. 

---

#### DATABASE and TABLES CREATION
    
The create_tables.py and DROP and CREATE queries of the 
sql_queries.py script are completed to create "sparkifydb" database with songplays,
users, songs, artists, time tables. The running of create_tables.py have to be done.

The running test.ipynb is recommended after running create_tables.py 
to confirm the creation of the tables with the correct columns.

---

#### ETL PROCESS

To develop ETL processes for each table was recommended the following to 
instruction in the etl.ipynb notebook and running test.ipynb at the end of each section
to confirm that tables were successfully filled. Also was recommended 
"to rerun create_tables.py to reset your tables before each time you run this notebook".

The pincipal part of the ETL development is the etl.py script.
The etl.ipynb and test.ipynb scripts are auxiliary. They are designed only for using by 
for the data engineer during project development.

The etl.py facilitates the processing of the entire datasets.
It reads files from song_data and log_data JSON files and loads them into 
corresponding tables. 
To complete etl.py development the INSERT queries and SELECT finding songs query
of the sql_queries.py are filled.

---

#### DATABASE QUERIES examples

Here are some examples of the queries that can be used by startup analytical team to
analyze the complete dataset. They are simple and clear. They can be vey util because
created database and ETL pipline have been developed with the goal to optimize 
analytics.

For example,
 - SELECT count(song_id) FROM songs
 
 ![example of the log_data structure](images/project1a_query1.png)
 
 - SELECT count(user_id) FROM users
 
 ![example of the log_data structure](images/project1a_query2.png)
 
 - SELECT count(level) FROM users
 
 ![example of the log_data structure](images/project1a_query3.png)
 
 

---
