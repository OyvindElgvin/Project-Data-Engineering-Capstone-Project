



# United States Immigration study

## Data Engineering Capstone Project

### Project Summary
This project aims to unveil information about immigration into the United States. Questions like how the flow of immigrants changes seasonally, if the destinations are dependent on temperature, and where the immigrants come from are some of the key questions this study is trying to answer.

Data are extracted from three different dataset sources, "I94 Immigration Data" from U.S. National Tourism and Trade Office, "Global Land Temperatures By City" from kaggle, and finally "Us Cities Demographics" from OpenSoft.

Apache Spark, Jupyter Notebook, and potentially Amazon Elastic MapReduce, EMR, are used to accomplish this ETL pipeline, which then writes parquet files either to an Amazon S3 bucket or locally to disk.

#### The project follows the following steps:
- Step 1: Scope the Project and Gather Data
- Step 2: Explore and Assess the Data
- Step 3: Define the Data Model
- Step 4: Run ETL to Model the Data
- Step 5: Complete Project Write Up




### Step 1: Scope the Project and Gather Data

#### Scope

The scope for this project is to create a Data Lake using an ETL pipeline. This Jupyter Notebook presents the whole process, describing and assessing the datasets, and at the bottom, running the complete ETL pipeline.  Ideally, this notebook should run on an EMR cluster in AWS, especially if using the whole immigration dataset. For this study, the star schema is chosen, making it possible to execute the various queries outlined below.  


### The choice of tools and technologies.

Apache Spark and Jupyter Notebook are the primary processing tools for this ETL pipeline. The ability for Spark to handle vast amounts of data in parallel, and the fact that AWS EMR supports Spark out of the box, makes Spark a compelling choice. The Jupyter Notebook is not only a great choice as an interactive environment for writing and running code but also works well as a tool for documenting.


#### Describing the datasets

- **I94 Immigration Data**: comes from the [U.S. National Tourism and Trade Office](https://travel.trade.gov/research/reports/i94/historical/2016.html) and contains  
various statistics on international visitor arrival in the USA and comes from the US National  
Tourism and Trade Office. The dataset contains data from 2016.


- **Temperature Data**: comes from [Kaggle](https://www.kaggle.com/berkeleyearth/climate-change-earth-surface-temperature-data) and is provided in a csv format, and includes  
information like average temperature, date, and city.


- **Demographic Data:** comes from [OpenSoft](https://public.opendatasoft.com/explore/dataset/us-cities-demographics/export/) and contains information about the demographics of all US cities such as race, male and female population, and median age. This dataset is from 2015.


#### This study tries to build an ETL and Data Model to answer the following questions:

- What cities in the U.S. are the most common for arriving immigrants?
- How does immigration change throughout a year?
- Where do the immigrants come from?
- What is the gender ratio for the immigrants?
- What is the age profile for the volume of immigrants?
- What is the legal status of immigrants in the U.S.




### Step 3: Define the Data Model
#### 3.1 Conceptual Data Model

As explained in the scope of the project, the Data Model is a Data Lake leading into a star schema. As opposed to a Data Warehouse, where each schema is normalized and tailored to a specific query through the 'schema-on-write' approach, the Data Lake uses a 'schema-on-read' approach, which gives more flexibility to what queries could be made.

To meet the requirements for the scope of the project a star schema with two dimensions is being made. The fact table consists of facts about the immigrants, and the two dimension tables will store information about temperatures per month per city and demographics for each city. This setup will hopefully allow analysts to make queries that answer the questions presented in the scope of the project.




![Bilde](/img/Table_diagram.png)



#### Immigration fact table
- id
- arrival_city
- arrival_state
- arrival_date
- departure_date
- year
- month
- country_citizenship
- country_residence
- age
- gender
- reason_for_immigration
- visatype



#### Temperature dimension table
- city
- date
- year_plus_4_years
- month
- average_temperature
- average_temperature_uncertainty




#### Demographics dimension table
- city
- state_code
- race
- median_age
- male_population
- total_population
- number_of_veterans
- foreign_born
- average_household
- count



### 3.2 Mapping Out Data Pipelines



#### Transformation steps for the Immigration Data
- Date values need to be transformed to YYYY-MM-DD.
- Port values need validating.
- States need to correspond to the state codes in the Demographic Data.
- Filter i94visa column by mode of transportation to only let travel by air.
- Set the reasons for immigration.
- Filter genders.
- Capping age at 105
- Convert i94res codes to new country_residence column
- Convert i94cit codes to new country_citizenship column



#### Temperature Data transforming steps
- Filter only for US using pandas
- Drop rows with NaN in the AverageTemperature column
- Convert dt to datetime
- Only keep records from 2013
- For each city keep only one records per day
- Make city names upper case to match fact table




#### Demographic Data transforming steps
- Make city names upper case
- Drop NaNs


### Step 4: Run Pipelines to Model the Data
#### 4.1 Create the data model


The ETL pipeline will consist of the following sections:
- #### Extraction
    - Extraction of csv and sas files
    - Extraction for dictionaries from the I94_SAS_Labels_Descriptions
- #### Transformation
    - Transforming of the Immigration Data
    - Transforming of the Temperature Data
    - Transforming of the Demographic Data
- #### Loading
    - Loading of the data to S3 or locally






	### Step 5: Complete Project Write Up
	* The rationale for the choice of tools and technologies is that Spark and EMR fit perfectly for this project. In my opinion, a notebook like this is pretty necessary to at least set up the ETL. The ETL itself could, of course, be a python script file, which then gets submitted to the EMR cluster, but that is outside of the scope of this project. The built-in parallelization in Spark makes it an ideal tool for working with large amounts of data.


	#### Scheduling
	* Since the data is aggregated in months, the ETL should be scheduled to run the first or the second day of every month.


	#### What would change if the data was increased by 100x?
	* If the data was increased by 100x, it would still make sense to run a script of the ETL in an AWS EMR. The EMR cluster would need to be larger, and the data would need to be stored in an S3 instead of in the EMR.


	#### What would change if the data populates a dashboard that must be updated daily by 7 am every day?
	* If the ETL needed to run every morning, it would make sense to set up a job in Apache Airflow and schedule it every morning. This would be an easy way to automate the scheduling and the data quality validating.


	#### What would change if the database needed to be accessed by 100+ people?
	* The more people accessing the data, the more CPU resources would need to be allocated to give a fast user experience. Instead of increasing the size of the EMR cluster running Spark, it would be more reasonable to write partitioned parquet files to a distributed database, like the Hadoop Distributed File System, HDFS, to secure faster query results for each user.

















create EMR cluster  
make a notebook  
upload that notebook  
remove SparkSession creation  
run  
