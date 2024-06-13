# PLP-DD-PwS-WK08-Data-Analysis
PLP Database Design &amp; Programming with SQL - Week 08 _ Data Analysis Assignment 

TASK 1: A brief explaination of any difficulty about my chosen dataset. 
I experienced the following challenges: 
(a) Importing the "Netflix Shows" dataset into MySQL Workbench as I did not take note of the difference bewteen the 'database' and the 'table'. 
(b) With respect to the challenge highligted in (a) above, I further spent more time trying to execute the SELECT * FROM command utilizing the title of the database rather than that of the table. 

Task 2: A brief explaination of interesting thing I noticed about bout my chosen dataset. 
The workbook is saved in the "comma-delimited (.csv) format which is associated with the loss of some features that would otherwise be preserved was the dataset saved in an Excel file format. 

Task 3: Data Fun
(a) Use simple SQL queries to play with the data.
I performed the following SQL queries on this data: 
(i) Find all movies released in 2021.
(ii) List all TV shows directed by a specific director.
(iii) Count the number of shows by rating.
(iv) List the top 10 countries with the most shows.

I converted the DataFrame to a SQL table and executed the queries above:
import sqlite3

# Create an in-memory SQLite database
conn = sqlite3.connect(':memory:')

# Load the DataFrame into a SQL table
netflix_data.to_sql('netflix', conn, index=False, if_exists='replace')

# Query 1: Find all movies released in 2021
query1 = """
SELECT title, director, cast, country, rating, duration, listed_in, description
FROM netflix
WHERE type = 'Movie' AND release_year = 2021
"""

# Query 2: List all TV shows directed by a specific director (example: 'Kirsten Johnson')
query2 = """
SELECT title, cast, country, rating, duration, listed_in, description
FROM netflix
WHERE type = 'TV Show' AND director = 'Kirsten Johnson'
"""

# Query 3: Count the number of shows by rating
query3 = """
SELECT rating, COUNT(*) as count
FROM netflix
GROUP BY rating
ORDER BY count DESC
"""

# Query 4: List the top 10 countries with the most shows
query4 = """
SELECT country, COUNT(*) as count
FROM netflix
GROUP BY country
ORDER BY count DESC
LIMIT 10
"""

# Execute the queries
movies_2021 = pd.read_sql_query(query1, conn)
tv_shows_directed_by_kirsten_johnson = pd.read_sql_query(query2, conn)
shows_by_rating = pd.read_sql_query(query3, conn)
top_10_countries_with_most_shows = pd.read_sql_query(query4, conn)

# Close the connection
conn.close()

movies_2021.head(), tv_shows_directed_by_kirsten_johnson.head(), shows_by_rating, top_10_countries_with_most_shows
​

(b) Find 2 cool facts hidden within the data (e.g., most popular interests). 
I explored the data to find the following interesting facts: 
(i) The most common genres listed for Netflix shows.
(ii) The country that produces the most Netflix content.
(iii) The most frequent director or actor in Netflix shows.

I performed the following analyses: 
# Load the CSV file into a DataFrame
netflix_data = pd.read_csv('/mnt/data/netflix_titles.csv')

# Check the most common genres
most_common_genres = netflix_data['listed_in'].value_counts().head(10)

# Check the country that produces the most Netflix content
most_content_country = netflix_data['country'].value_counts().head(10)

# Check the most frequent director
most_frequent_director = netflix_data['director'].value_counts().head(10)

# Check the most frequent actor
most_frequent_actor = netflix_data['cast'].value_counts().head(10)

most_common_genres, most_content_country, most_frequent_director, most_frequent_actor



(c) Use basic SQL queries like (COUNT, AVG, and SUM) to understand more about the data you have.
I utilized basic SQL queries like COUNT, AVG, and SUM to extract some insights from the dataset. I started by loading the data and converting it into a format that I could query using SQL. 

import pandas as pd
import sqlite3

# Load the CSV file into a DataFrame
file_path = '/mnt/data/netflix_titles.csv'
netflix_data = pd.read_csv(file_path)

# Create an in-memory SQLite database
conn = sqlite3.connect(':memory:')

# Load the DataFrame into a SQL table
netflix_data.to_sql('netflix', conn, index=False, if_exists='replace')

# Basic SQL queries
queries = {
    "count_total_shows": "SELECT COUNT(*) as total_shows FROM netflix",
    "average_release_year": "SELECT AVG(release_year) as average_release_year FROM netflix",
    "count_by_type": "SELECT type, COUNT(*) as count FROM netflix GROUP BY type",
    "count_by_rating": "SELECT rating, COUNT(*) as count FROM netflix GROUP BY rating",
    "sum_of_duration": """
        SELECT SUM(CAST(SUBSTR(duration, 1, INSTR(duration, ' ') - 1) AS INTEGER)) as total_duration_minutes
        FROM netflix
        WHERE type = 'Movie' AND duration LIKE '% min'
    """
}

# Execute the queries
results = {name: pd.read_sql_query(query, conn) for name, query in queries.items()}

# Close the connection
conn.close()

results



Ask Away (30 pts):
(a) Formulate 2 questions about the data (e.g., what are popular shows in different countries?).
Here are two interesting questions I formulated about the data:
(i) What are the most popular genres of Netflix shows in different countries?
(ii) Who are the most frequently featured actors in Netflix shows, and how does this vary by genre?

(b) Write basic SQL queries (WHERE, ORDER BY) to find answers.
Question 1: What are the most popular genres of Netflix shows in different countries?

This query will group shows by country and genre, counting the number of shows in each genre per country, and then order the results by country and the number of shows:  
SELECT country, listed_in, COUNT(*) as show_count
FROM netflix
GROUP BY country, listed_in
ORDER BY country, show_count DESC;

Question 2: Who are the most frequently featured actors in Netflix shows, and how does this vary by genre?
This query will group shows by actor and genre, counting the number of shows each actor has appeared in per genre, and then order the results by genre and the number of shows: 
SELECT cast, listed_in, COUNT(*) as show_count
FROM netflix
WHERE cast IS NOT NULL
GROUP BY cast, listed_in
ORDER BY listed_in, show_count DESC;


These queries assume the table is named netflix and that the columns country, listed_in, and cast are present as in the provided dataset. The ORDER BY clause sorts the results first by country or listed_in and then by the count of shows in descending order to show the most popular genres or actors at the top. 


(c) Share what you learned from the answers.
Question 1: Popular Genres by Country
SQL Query: SELECT country, listed_in, COUNT(*) as show_count
FROM netflix
GROUP BY country, listed_in
ORDER BY country, show_count DESC;

Insights:
(i) Popular Genres: The results will show which genres are most prevalent in each country. For instance, if the United States has a high count for Documentaries, it suggests that documentaries are a popular genre there.
(ii) Regional Preferences: By comparing the top genres across countries, we can see how content preferences vary. Some countries might favor TV Dramas, while others may prefer Comedy or Action & Adventure.
(iii) Content Strategy: Netflix can use this information to tailor their content acquisition and production strategies to match regional tastes, potentially increasing subscriber satisfaction and engagement. 


Question 2: Most Frequently Featured Actors by Genre
SQL Query: SELECT cast, listed_in, COUNT(*) as show_count
FROM netflix
WHERE cast IS NOT NULL
GROUP BY cast, listed_in
ORDER BY listed_in, show_count DESC;

Insights:
(i) Popular Actors: The results will highlight which actors appear most frequently in Netflix shows and within specific genres. For example, if an actor appears repeatedly in Action & Adventure, it indicates they are a significant presence in that genre.
(ii) Genre Dominance: Some actors might dominate certain genres, which could be useful for casting decisions in new projects. Producers can identify which actors are well-received in specific genres.
(iii) Star Power: Understanding the most frequently featured actors can help Netflix leverage star power in marketing and promotional efforts. Shows with popular actors can be highlighted to attract viewers.


Presentation Power (20 pts):
Prepare a short pitch deck presentation to showcase your work:
Introduce the dataset and your goals.
Briefly explain the import process and the interesting thing you found.
Share the 2 cool facts you discovered.
Discuss your questions, SQL queries, and what you learned.
Present your charts and explain their meaning.
Briefly summarise your project.
https://gamma.app/docs/Unveiling-Insights-A-Journey-Through-Netflix-Movies-Data-flkuyun05viyb5l?mode=doc









