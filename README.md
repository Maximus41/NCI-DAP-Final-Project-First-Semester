# DAP-Project-First-Semester-NCI
Project Source code for DAP Module - 1st Semester - MSc in Data Analytics - National College of Ireland
<h4>Steps to run the code:</h4>
<ul><b>1 -></b> Open <b>Anaconda Command Prompt</b></ul>
<ul><b>2 -></b> Goto Project folder</ul>
<ul><b>3 -></b> Run the command <b>"conda env create -f environment.yml"</b> to create conda environment</ul>
<ul><b>4 -></b> Run <b>"conda activate DAP"</b> to activate the environment</ul>
<ul><b>Note :<i></b> <b>MongoDB</b> and <b>PostgreSQL</b> should be already installed in the system to successfully run the code.</i></ul>


## Objective
Various study groups and institutions have 
conducted several studies on the criteria for success in the 
film business to date. According to one study, there are three 
factors by which a film's success can be measured. These are 
ratings given to a film by viewers and reviewers, as well as 
the picture's financial performance in its first few weeks and 
the number of awards it receives[1]. Using **ETL (*Extraction, Transformation and Loading*)** techniques, this project will organize various sources of unstructured data into structured format inorder to analyze 
datasets on the film industry based on aforementioned criterion. To 
extract information from the data, the project will use **EDA (*Exploratory Data Analysis*)** techniques available. The main objectives of EDA 
can be summarized as follows:
+ **Understand the dataset**
+ **Establish relationships between attributes**
+ **Find out abnormalities in the data**

## Methodology
Data collecting is the most important element of data 
analysis. This section mentions different types of data sources 
used in the project and takes through the different steps of 
the methodology employed to prepare them for exploratory 
analysis. The steps discussed here are taken from the KDD 
methodology used for the project. The following is a detailed 
description of the steps:

### *A.* Data Selection
The data was selected from four different sources. The 
sources are detailed in the table below **(Table 1)**

| Name | Type | Format | Tables | Link |
| :---- | :----: | :----: | :----: | :----: |
| Movielens | CSV | Structured | 4 | [Link](https://movielens.org/) |
| The Movie Database| API | JSON | NA | [Link](https://developers.themoviedb.org/3/movies/get-movie-details) |
| The Rapid API | API | JSON | NA | [Link](https://rapidapi.com/rapidapi/api/movie-database-alternative/) |
| Oscars| Website | Dictionary | NA | [Link](https://www.oscars.org/) |

***Table 1 : Data Sources***

Details of the tables used from the ***MovieLens*** Dataset are 
mentioned in the table **(Table 1.1)** below:

| Name | Attributes| Description |
| :---- | :----: | :----: |
| movies.csv | movieId, title, genre | Most basic details of the movie |
| links.csv | movieId, imdbId, tmdbId| Standard Imdb id and Tmdb id mapped to the movieId |

***Table 1.1 : MovieLens CSV Tables***

### *B.* Data Pre-processing
Jupyter Notebook was used to write and run the code 
for the entire project, using conda as its package manager. 
The packages used in the project for ETL and EDA are listed below:
Pandas
- **Pymongo**
- **Urllib3**
- **Selenium**
- **Psycopg2**
- **Sqlalchemy**
- **Seaborn**
- **Matplotlib**
- **missingno**

The conda environment containing the above mentioned packages were created from the *environment.yml* file by executing the following commands in *Conda prompt*:

```
• conda env create -f environment.yml
• conda env update -f environment.yml
• conda install ipykernel
• ipython kernel install --user --name=DAP
```

The final master dataset was obtained from all the four data sources following the ***ETL*** steps described below:

***Step 1 :*** \
The CSV files from the MovieLens dataset, was used to create a sorted list of 2000 movies released 
between 2009 to 2018. The data was sorted using the year 
value taken from the ***'title'*** column in the ***'movies.csv'*** file. 
Then, on the basis of ***'movieId'***, attribute, records from both the tables
***‘movies’*** and ***‘links’*** were merged to create a single collection. 
This combined table were then iteratively traversed to retrieve 
details from the APIs previously listed using the corresponding 
external ids.

***Step 2:***\
Both API responses have attributes that are both common 
and distinctive. They were both called to obtain detailed 
information about the films, and their responses were saved in 
MongoDB as JSON documents. In the data pipeline, 
MongoDB served as a good staging destination, allowing 
room to resume without having to contact the APIs again if 
data was corrupted during subsequent phases. The persistence 
of the data download state was another instance where 
MongoDB came through without any effort. One of the APIs 
has a daily limit of 1000 calls; thus, a JSON document was 
created to retain information about the latest state of the Http 
requests, which was then saved as a document in a separate 
collection titled 'State' in the MongoDB database.

***Step 3:***\
The final source of data provides raw data on Oscar wins 
and nominations under the categories of "Best 
Picture", "Directing", "Acting," and "Writing" for the years 
spanning between 2002 and 2018. The website for Academy 
Awards was scraped using Selenium to obtain this 
information. Selenium is a GUI testing tool that can automate 
web page clicks and extract data from the HTML content. The 
scraped data was then converted to JSON and placed in a 
MongoDB database as documents.

***Step 4:***\
ERDs (Entity Relationship Diagrams) were 
conceptualized to create tables in PostgreSQL with the data,
now available in MongoDB. By the end of this step, 6 SQL 
schemas and tables were created. These schemas are Movie, 
Director, Writer, Actor, Language, Genre, and 
Academy_awards. ‘Movie’ and ‘Academy_awards’ are the 
main tables whereas rest of them are multivalued attributes for 
‘Movie’.

***Step 5:***\
In the final step, following the creation of requisite SQL 
tables, the no-SQL documents were retrieved from the 
MongoDB database and transformed to corresponding Pandas
data frames as a preparatory stage before inserting data into 
the SQL database. The python package used for the purpose 
was 'sqlalchemy'. This Python library makes it easy to insert 
large amounts of data simultaneously into a PostgreSQL 
database. This is a quick operation for the project's 
relatively tiny amount of data (about 2k rows).

### *C.* Data Transformation
Outliers were initially detected and deleted in this step. In 
addition, null values were substituted with appropriate values. 
Finally, feature engineering was performed on the main data 
frame produced from the SQL table 'Movie' to include crucial 
features. Several multivalued properties, such as languages, 
genres, writers, directors, and productions, were maintained in 
their own relational tables in this database. The following 
dichotomous (Table 2) and continuous features (Table 2.1)
were created using the information from these multivalued 
attribute tables and the academy_awards:


| Name | Description |
| :---- | :----: |
| **oscar_nominated_director** | Indicates whether any of the directors has been nominated for an Academy Award.|
| **oscar_nominated_actor** | Indicates whether any of the actors has been nominated for an Academy Award |
| **oscar_nominated_actress** | Indicates whether any of the actresses has been nominated for an Academy Award. |
| **oscar_nominated_writer** | Indicates whether any of the writers has been nominated for an Academy Award.|

***Table 2 : New Categorical Features***


| Name | Description |
| :---- | :----: |
| **number_of_directors_in_a_movie** | The total number of directors engaged in a film|
| **number_of_writers_in_a_movie** | The total number of writers engaged in a film |
| **number_of_producers_in_a_movie** | The total number of producers engaged in a film |

***Table 2.1 : New Continuous Features***

As shown below, new pandas dataframes were created by 
merging information from multiple tables: 

+ Number of movies grouped by producers between 
2009-2018
+ Number of movies grouped by languages between 
2009-2018
+ Number of movies grouped by genres between 2009-
2018
+ Number of movies grouped by censor certificates 
between 2009-2018

### *D.* Exploratory Data Analysis
Exploratory data analysis (EDA) is a step in the data 
mining process in which an analyst does statistical and 
visual analysis on processed data to find patterns, 
loopholes, and information that would otherwise be 
hidden owing to its complexity and volume. The 
categories under which EDA is carried out in the project 
are listed below:

+ **Univariate Non Graphical :** Five Number Summary, 
Mean, Median, Mode, Standard deviation, skewness, 
kurtosis
+ **Univariate Graphical :** Boxplot, Histogram, Barchart,
+ **Multivariate Graphical**: Scatter plot, parallel boxplot,
pie charts


## Results And Evaluation
The dataset was subjected to a number cleaning, 
transformation and exploratory analyses in the order below:

### *A.* Null and Zero Values
The data didn't have many null values, as shown in the 
figure (Fig 1). However, several columns, such as 'budget' 
and 'revenue,' contained numerous zero values that were 
substituted with the median value.

### *B.* Outliers
Boxplots of all continuous variables were generated, 
revealing many data outliers. Subsequently, the number of 
outliers for each of the variables was estimated using the IQR 
(Inter Quartile Range) formula. The number stood out to 
be extremely high, and therefore, in order to prevent 
a substantial amount of data loss, those were left untouched.

### *C.* Descriptive Analysis
The descriptive statistics of the pertinent variables were 
recorded after that using Five Number Summary and
calculating standard deviation around the mean, skewness 
and kurtosis. Those data are presented below:

Each variable's skewness and kurtosis coefficient were 
also calculated as already mentioned. The calculated result is 
consistent with earlier observations of outliers in boxplots and 
histograms. Almost every variable is heavily skewed to the 
right. Except for 'imdb_rating’, ‘metascore', 'rotten 
tomatoes', and 'vote_average', all variables were found to be 
leptokurtic (> 3). Three of the above variables are 
platykurtic (3), and one is mesokurtic (3). These findings 
suggest a significantly asymmetrical distribution of values in 
the sample.

### *D.* Feature Engineering
As previously discussed in a data preprocessing phase, 
seven new features were built from existing data. These 
features make it possible to determine a film's commercial and 
critical success based on the number of Oscar-nominated crew 
members who appear in the film as an actor or a staff. 

### *E.* Parallel Boxplots
Using 'oscar_win' and 'oscar_nomination,' as 
factors two complementary boxplots for 'runtime' and 'award 
wins' were plotted. This produced few interesting details:
+ 95% of Oscar-nominated films have a running time 
of 1.5 to 2 hours approximately
+ All the Oscar-winning films were between 1.5 and 
2 hours long.
+ All the Oscar-winning films had previously won at 
least 100 awards in different categories at other 
events.
+ Almost 95% of all films, excluding Oscar 
nominees, have only garnered a total of 25 awards.


### *F.* Histogram
The following details were revealed from histograms 
of the continuous variables:

+ **runtime :** The majority of the films were 1 to 1.5 
hours long.
+ **award_wins :** Almost all of the films garnered fewer 
than 25 awards
+ **award_nominations :**  The majority of films receive 
25 to 50 nominations.
+ **budget :** 80% of films have a budget of less than $50 
million dollars.
+ **revenue :** Almost 90% of films grossed less than 
$250 million dollars.
+ **imdb_rating :** Although the data is skewed to the 
left, the majority of scores are between 5.5 and 8..
+ **metascore :** In Metacritic, nearly 35% to 45% of 
films receive a 60% rating.
+ **rotten_tomatoes :** The data is more dispersed here, 
with only about a quarter of the films scoring 60% to 
65%.
+ **vote_average :**  Again, this is in line with other online 
sites. For over 40% of the films, the Tmdb ratings are 
between 6 and 7.

### *G.* Covariances of pertinent variables
For continuous variables, Pearson's correlation matrix was 
produced to measure the direction and strength of relationship 
between them. The results revealed that three variables, 
'revenue', 'budget', and 'popularity', had a substantial 
positive association with 'vote count'. The correlation 
between 'revenue' and 'popularity' is clear and obvious. The 
relationship between 'budget' and 'vote count' could indicate 
that audiences are more interested in higher-budget films. The
results can be viewed in Fig 3



### *H.* Covariance
The dataset's covariance matrix is produced, and the following 
odd negative covariances are discovered (Table 4):

|Variables|metascore|rotten_tomatoes|oscar_win|budget|
|:----|:----:|:----:|:----:|:----:|
|metascore|243.14|--|--|--|
|rotten_tomatoes|347.64|650.12|--|--|
|oscar_win|0.1|0.1|0|--|
|budget|-40570344|-101968741|-42967|2.43e^15|

***Table 4 : Covariances of pertinent variables***

### *I.* Pie Charts
Movie distribution by censor certificate:(Fig 4)
+ Almost two third of all films produced are labelled
as 'R' or 'PG-13'.
+ Around 39% of all films were rated 'R', while 26% 
were rated 'PG-13'. This indicates that 'R' and 'PG-13' rated films are the most popular 
categories among moviegoers.

Movie distribution by Language (Fig 4.1):
+ Nearly 64% of all the films were released in English.
+ Only 6 % movies were released in French which 
comes second
+ Other languages account for 21% of total releases.

Movie distribution by Genre (Fig 4.2):
+ 'Drama' is the most common genre found in most of 
the films. It was found in 13% of all the films that 
were released.

Movie distribution by Production Houses (Fig 4.3):
+ The top 10 production companies made 10% of all 
films.
+ **'Universal Pictures'**, **'Warner Bros.'**, **'Columbia Pictures'**, and **'20th Century Fox'** are among the top 
10 producers, accounting for 5% of all films.


### *J.* Bar Charts
Bar charts for all the categorical variables were plotted 
and the following observations were made:
+ Every year, with the exception of 2015, there has 
been a steady production of films.
+ Below 10% (< 200) films had at least one Oscar-nominated directors in the team
+ Almost 10% (= 200) films had at least one Oscar-nominated writer in the team
+ Slightly more than 10% (> 200) films had at least one 
Oscar nominated lead actor in the team.
+ Almost 5% (= 100) films had at least one oscar 
nominated lead actress
+ Almost 10% (= 200) movies had at least two 
directors in the team.
+ Almost 1/3rd of all movies had 1, 2 and 3 writers. In 
a film, the highest number of writers ever appeared 
happens to be around 20.
+ In a film, the highest number of producers ever 
involved happens to be around 21

Bar charts for all categorical variables against 
budget was plotted and following observations were 
recorded:
+ The average budget for films rated 'PG', 'PG-13', and 
'G' was the highest.
+ Films made in 2018 were more expensive than films 
made in previous years.
+ The cost of films directed by or starring Oscar 
nominated actors is approximately 100% more. It 
suggests that directors and actors who have been 
nominated for an Academy Award receive more pay.
+ Surprisingly, films starring Oscar nominated 
actresses had lower budgets.
+ Films with more than three directors cost around half 
as much as other films.
+ The most expensive films are those with 3 writers in 
the team.

Grouped Bar Chart displaying Number of films
produced factored by censor certificate and oscar win 
were plotted and following observation was recorded
(Fig 5)
+ The majority of the Oscar nominations came from 
films with a 'R' or 'PG-13' rating.


### *K.* Line Graphs
Number of ‘R’, ‘PG-13’ and ‘PG’ films produced over the 
period of 10 years (2009 - 2018) were plotted (Fig 6) using 
line plot and the following observation was made.
+ From 2009 through 2018, 'R' films dominated the 
film industry.
+ In 2011, the number of 'R' and 'PG-13' films released 
was nearly equal.
+ After 2016, there was a decline in the production of 
films in all three categories.
+ In 2018, the number of 'R' films produced fell below 
that of 'PG-13' films.

To track the overall budget and revenue of the films over 
years, a line plot (Fig 6.1) was created. Following 
observation was made:

+ The revenue line remained consistently large in 
comparison to the budget line, implying that the 
industry is profitable and reliable.
+ Following 2016, there appears to be a drop in both 
budget and revenue.

### *L.* Scatter Plots
Budget vs Award Wins:
+ There is no relationship between a film's budget and 
the number of awards it receives.


## CONCLUSIONS AND FUTURE WORK

### A. Conclusions
Following strategies for creating successful movies 
could be offered after comprehensive examination of the 
data:

+ Keep the film’s running time between 1.5 and 2 
hours.
+ There appears to be a positive relationship between 
budget and popularity of the film among audiences. 
Increasing the budget of a film or clever marketing 
might have an impact on its commercial success.
+ Focus more on producing ‘R’, ‘PG-13’ and ‘PG’ 
rated movies.
+ Release films in ‘English’.
+ Incorporate 'Drama' aspects into the film.
+ Associate the film with major production houses.

### Future Work
+ It's reasonable to investigate award winners by genre 
to learn more about which genres are most popular 
with critics and awarding organizations.
+ The number of awards won is a deciding element in 
winning an Oscar. As a result, further research is 
needed to determine the elements that influence the 
number of awards a film receives.
+ More research into the relationship between 
production houses and financial variables needs 
to be done