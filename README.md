# Netflix Movies and TV Shows Data Analysis using SQL

![](https://github.com/najirh/netflix_sql_project/blob/main/logo.png)

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

## Business Problems and Solutions

### 1. Count the Number of Movies vs TV Shows

```sql
select COUNT(type),type from netflix Group by Type;
```

**Objective:** Determine the distribution of content types on Netflix.

### 2. Find the Most Common Rating for Movies and TV Shows

```sql
with t1 as(select count(rating),rating,type,dense_rank() over(partition by type order by count(rating)desc) from netflix
GROUP BY type,rating Order By type,Count(rating) desc)
select * from t1 where dense_rank=1;
```

**Objective:** Identify the most frequently occurring rating for each type of content.

### 3. List All Movies Released in a Specific Year (e.g., 2020)

```sql
with t1 as(select title,release_year,type from netflix
group by 2,3,1 order by 2,3)
select * from t1 where type='Movie' AND release_year=2020
Order by release_year desc;
```

**Objective:** Retrieve all movies released in a specific year.

### 4. Find the Top 5 Countries with the Most Content on Netflix

```sql
with t1 as(select count(*) as no_of_content,unnest(string_to_array(country,',')) as new_country from netflix
group by new_country order by count(*) desc)
select * from t1 LIMIT 5;
```

**Objective:** Identify the top 5 countries with the highest number of content items.

### 5. Identify the Longest Movie

```sql
with t1 as(select title,type,duration from netflix  where type='Movie' order by 3 Desc,2)
,t2 as(select t1.title,t1.duration from t1 where duration<>'null' group by 2,1 order by 2 Desc)
select * from t2 where duration='99 min';

---OR

select title,type,duration from netflix
where duration=(select MAX(duration) from netflix);
```

**Objective:** Find the movie with the longest duration.

### 6. Find Content Added in the Last 5 Years

```sql
select title,TO_DATE(date_added,'Month DD,YYYY') as new_date from netflix
where TO_DATE(date_added,'Month DD,YYYY')>=current_date -Interval'5 Years';
```

**Objective:** Retrieve content added to Netflix in the last 5 years.

### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

```sql
select type,title,director from netflix
where director LIKE '%Rajiv Chilaka%';
```

**Objective:** List all content directed by 'Rajiv Chilaka'.

### 8. List All TV Shows with More Than 5 Seasons

```sql
select type,title,duration from netflix
where duration NOT IN('1 Season','2 Seasons','3 Seasons','4 Seasons','5 Seasons') AND type='TV Show'
Group By duration,type,title
Order by duration desc;

---OR

select type,title,split_part(duration,' ',1)::numeric as seasons from netflix
where type='TV Show' AND split_part(duration,' ',1)::numeric>5
order by split_part(duration,' ',1)::numeric desc;
```

**Objective:** Identify TV shows with more than 5 seasons.

### 9. Count the Number of Content Items in Each Genre

```sql
select unnest(string_to_array(listed_in,',')) as genre,count(*) from netflix
group By unnest(string_to_array(listed_in,',')) order by count(*) Desc;
```

**Objective:** Count the number of content items in each genre.

**Objective:** Calculate and rank years by the average number of content releases by India.

### 10. List All Movies that are Documentaries

```sql
with t1 as(select Unnest(string_to_array(listed_in,',')) as c,type,title from netflix
where type='Movie')
select * from t1 where c='Documentaries';
```

**Objective:** Retrieve all movies classified as documentaries.

### 11. Find All Content Without a Director

```sql
select COALESCE(director,'no'),title,type from netflix
where COALESCE(director,'no')='no';
```

**Objective:** List content that does not have a director.

### 12. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

```sql
select type,casts,title,
EXTRACT(year from TO_DATE(date_added, 'Month DD,YYYY')) as new_year from netflix
where EXTRACT(year from TO_DATE(date_added, 'Month DD,YYYY'))>Extract(year from (current_date-Interval'10 years'))
AND casts ILIKE '%Salman Khan%' AND type='Movie';

---OR

select type,title,release_year from netflix
where release_year>extract(year from current_date)-10 AND casts ILIKE '%Salman Khan%';
```

**Objective:** Count the number of movies featuring 'Salman Khan' in the last 10 years.

## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.



## Author - Self

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles.

