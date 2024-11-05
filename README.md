
# Netflix Movies and TV Shows Data Analysis using SQL

## Project Overview: 

This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset.

## Problem Statement: 

    1) Analyze the distribution of content types (movies vs TV shows).
    2) Identify the most common ratings for movies and TV shows.
    3) List and analyze content based on release years, countries, and durations.
    4) Explore and categorize content based on specific criteria and keywords

### Tools Used: SQL to obtain the results by retrieving relevant results from the respective tables with the help of SQL queries and visualizing the relevant KPIs in the form of interactive elements for better understanding. 

### Steps Followed: 

- Step 1 : Review the dataset which is available as csv files in excel and analyse the number of column heads and the number of rows for the data. Get familiar with the data and spend some time analysing the column heads along with data types.  

- Step 2: Load data into PostgreSQL server by creating a new database and create a table with relevant columns along with their data types.

![Netflix 1](https://github.com/user-attachments/assets/23f57ace-a652-4061-a495-c3a1a12a6f8b)

Now, import the data by using Import option and do remember to toggle ON the Header field. 

#### SQL QUERIES: 

a) Count the Number of Movies vs TV Shows -  

    SELECT type, COUNT(*) AS total_content 
    FROM netflix_titles
    GROUP BY type;


![Netflix 2](https://github.com/user-attachments/assets/1fc08c70-eff2-4df3-acd2-8ca8fb9a3ca0)

b) Find the Most Common Rating for Movies and TV Shows: 


    SELECT
    type,
    rating
    FROM 
        (
        SELECT 
        type, 
        rating, 
        Count(*) as count,
        RANK() OVER(PARTITION BY type ORDER BY Count(*)DESC) AS Ranking
    FROM netflix

    GROUP BY type, rating
    ) AS t1
    WHERE Ranking = 1;

 Below is the snapshot of the query fired and the subsequent result:

![Netflix 3](https://github.com/user-attachments/assets/1a15264c-e161-4ef6-b146-b8010694c991)

c)  List All Movies Released in a Specific Year (e.g., 2020) - 

    SELECT * 
    FROM netflix
    WHERE type = 'Movie' AND release_year = 2020;

![Netflix 4](https://github.com/user-attachments/assets/b5911564-f1bb-49c2-a43a-36bb9b9559ff)

 d) Find the Top 5 Countries with the Most Content on Netflix  - 

    SELECT
	UNNEST(STRING_TO_ARRAY(country,',')) AS new_country,
	COUNT(show_id) AS total_content
	FROM netflix
	GROUP BY new_country
	ORDER BY total_content DESC
    LIMIT 5;

![Netflix 5](https://github.com/user-attachments/assets/63733508-6b14-4d84-b230-c9cc35efc16d)

e) Select the longest movie -
    
    SELECT *
    FROM netflix
    WHERE type = 'Movie'
    AND
    duration = (SELECT MAX(duration) FROM netflix);

![Netflix 6](https://github.com/user-attachments/assets/955bf1c9-f2ef-457c-b78f-fe7eaa26aca4)

There are around 118 movies with the longest duration of 99 minutes. 

f) Find content added in the last 5 years - 

    SELECT *
    FROM netflix
    WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE -   INTERVAL '5 years';

![Netflix 7](https://github.com/user-attachments/assets/c9330b0a-aa26-4369-a9a1-c91910d59c87)

g) Find All Movies/TV Shows by Director 'Rajiv Chilaka' -

    SELECT *
    FROM netflix
    WHERE director ILIKE '%Rajiv Chilaka%';

'ILIKE' function is used to make the the director name case sensitive. 

![Netflix 8](https://github.com/user-attachments/assets/da2ecd7e-8202-49ef-9190-eb5d966913ca)


h) List all the TV shows with more than 5 seasons - 

    SELECT *
    FROM netflix
    WHERE type = 'TV Show' 
    AND
    SPLIT_PART(duration, ' ', 1)::numeric > 5;



![Netflix 9](https://github.com/user-attachments/assets/103600e3-c530-4891-8a7b-f81e5492078d)


i) Count the number of items in each genre - 

    SELECT
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
    FROM netflix
    GROUP BY genre
    ORDER BY total_content DESC;

![Netflix 10](https://github.com/user-attachments/assets/d2e50c38-b9e7-4a6c-bf5a-f7e1f3876793)

j) Find each year and the average numbers of content release in India on netflix. Also return top 5 year with highest avg content release – 

    SELECT
    EXTRACT(YEAR FROM TO_DATE(date_added, 'Month, DD YYYY')) AS Year,
    COUNT(*) AS total_content,
    ROUND(COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100, 2) AS Avg_content 
    FROM netflix
    WHERE country = 'India'
    GROUP BY Year
    ORDER BY Year;

![Netflix 11](https://github.com/user-attachments/assets/1ca9aab4-74ee-41cf-bd3f-a00ecfc3483d)

k) List all movies that are Documentaries –

    SELECT * 
    FROM netflix
    WHERE listed_in ILIKE '%Documentaries'; 

![Netflix 12](https://github.com/user-attachments/assets/a1370bbf-369f-44c1-ba17-18696aec758b)

l) Find All Content Without a Director - 
    
    SELECT * 
    FROM netflix
    WHERE director IS NULL; 

![Netflix 13](https://github.com/user-attachments/assets/e2756681-52d4-4886-a19a-c176969605b5)


m) Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years - 

    SELECT * 
    FROM netflix
    WHERE casts LIKE '%Salman Khan%'
    AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;


![Netflix 14](https://github.com/user-attachments/assets/ff71f421-6d14-4969-872f-46652146ca51)


n) Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India -

    SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
    FROM netflix
    WHERE country = 'India'
    GROUP BY actor
    ORDER BY COUNT(*) DESC
    LIMIT 10;

    

![Netflix 15](https://github.com/user-attachments/assets/18b74aec-81ba-45dc-b4b4-3b8cdf7fd2c4)

o) Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords - 
  

    SELECT 
    category,
    COUNT(*) AS content_count
    FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
    ) AS categorized_content
    GROUP BY category;

![Netflix 16](https://github.com/user-attachments/assets/d1daf7c2-7591-4791-8521-b40e2e107b43)     

## Insights:

a) Netflix's majority of content was of the 'movie' type(6131), compared to the 'TV Show' type(2676). 

b) International Movies were the top genre among the dataset with significant titles listed under this category, followed by Dramas and Comedy genres respectively. 

c) A notable portion of the content was added in recent years, showing a strong trend toward new releases.

d) In country-wise analysis, the United States had the most releases, with 2818, followed by India(972) and the United Kingdom(419). 

e) "TV-MA" was the most common rating for titles in both the movie and TV Show types. 

f) Anupam Kher was the actor w.r.t India, who has the most number of releases on Netflix with 36 titles under his name. 
