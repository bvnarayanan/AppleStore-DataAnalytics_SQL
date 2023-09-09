-- Check for Discreptancy --
SELECT COUNT(DISTINCT id) AS UniqueAppIds
FROM AppleStore

SELECT COUNT(DISTINCT id) AS UniqueAppIds
FROM appleStore_description

-- Check for missing values in key fields -- 
SELECT COUNT(*) AS MissingValues
FROM AppleStore
WHERE track_name IS null OR user_rating IS null OR prime_genre IS NULL

SELECT COUNT(*) AS MissingValues
FROM appleStore_description
WHERE app_desc IS null 

-- number of Apps per genre -- 

SELECT prime_genre, COUNT(*) AS NumApps
FROM AppleStore
GROUP BY prime_genre
ORDER BY NumApps DESC

-- OVERVIEW OF APP RATINGS -- 
SELECT min(USER_RATING) AS MinRating, max(user_rating) AS MaxRating, avg(user_rating) AS AvgRating
FROM AppleStore




-- INSIGHT 
-- distribution of app prices --
WITH CTE AS (
    SELECT 
        (price / 2) * 2 AS PriceBinStart,
        ((price / 2) * 2) + 2 AS PriceBinEnd
    FROM AppleStore
)

SELECT 
    PriceBinStart,
    PriceBinEnd,
    COUNT(*) AS NumApps
FROM CTE
GROUP BY PriceBinStart, PriceBinEnd
ORDER BY PriceBinStart;
-- DATA ANALYSIS --



SELECT App_Type, AVG(user_rating) AS Avg_Rating
FROM (
    SELECT 
        CASE
            WHEN price > 0 THEN 'Paid'
            ELSE 'Free'
        END AS App_Type,
        user_rating
    FROM AppleStore
) AS Subquery
GROUP BY App_Type;

--Check if apps with more supported languages have higher ratings
SELECT language_bucket, AVG(user_rating) AS Avg_Rating
FROM (
    SELECT 
        CASE 
            WHEN lang_num < 10 THEN '<10 languages'
            WHEN lang_num BETWEEN 10 AND 30 THEN '10-30 languages'
            ELSE '>10 languages'
        END AS language_bucket,
        user_rating
    FROM AppleStore
) AS Subquery
GROUP BY language_bucket
ORDER BY Avg_Rating DESC;

-- Genres with Low Ratings
SELECT TOP 10 prime_genre, AVG(user_rating) AS Avg_Rating
FROM AppleStore
GROUP BY prime_genre
ORDER BY Avg_Rating ASC;

-- Check if there is correlation between the length of app description and user rating

SELECT description_length_bucket, AVG(average_rating) AS average_rating
FROM (
    SELECT CASE 
                WHEN LEN(b.app_desc) < 500 THEN 'Short'
                WHEN LEN(b.app_desc) BETWEEN 500 AND 1000 THEN 'Medium'
                ELSE 'Long'
            END AS description_length_bucket,
            a.user_rating AS average_rating
    FROM AppleStore AS a
    JOIN appleStore_description_combined AS b 
    ON a.id = b.id
) AS subquery
GROUP BY description_length_bucket
ORDER BY average_rating DESC;

--Check the top-rated apps for each genre

SELECT prime_genre, track_name, user_rating
FROM ( 
    SELECT prime_genre, track_name, user_rating,
    RANK() OVER(PARTITION BY prime_genre ORDER BY user_rating DESC, rating_count_tot DESC) AS rank
    FROM AppleStore
) AS a 
WHERE
a.rank = 1