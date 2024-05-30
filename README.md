# Learn SQL
If you haven't already make sure you watch [this video](https://youtu.be/p3qvj9hO_Bo) which will teach you all the basics of SQL in 60 minutes.

After watching the video try to complete the exercises listed below using the data provided in this repository.

All of the solutions are available in the repository, and [this video](https://youtu.be/30W5wjgJR08) goes over all of the solutions.

## Setup
First drop your existing database that was created in the tutorial. `DROP DATABASE record_company;`

Copy the code inside the [schema.sql](schema.sql) file, paste it into MySQL Workbench, and run it. (This file contains the code necessary to create and add the tables from the tutorial video)

## Exercises
### 1. Create a Songs Table


This table should be called `songs` and have four properties with these exact names.
1. `id`: An integer that is the primary key, and auto increments.
2. `name`: A string that cannot be null.
3. `length`: A float that represents the length of the song in minutes that cannot be null.
4. `album_id`: An integer that is a foreign key referencing the albums table that cannot be null.


## My solution: 
```
CREATE TABLE songs (
	id SERIAL PRIMARY KEY, 
	name VARCHAR(255) NOT NULL, 
	length FLOAT NOT NULL, 
	album_id INT REFERENCES albums(id)
);
```


### 2. Select only the Names of all the Bands

Change the name of the column the data returns to `Band Name`

| Band Name         | 
|-------------------| 
| Seventh Wonder    | 
| Metallica         | 
| The Ocean         | 
| Within Temptation | 
| Death             | 
| Van Canto         | 
| Dream Theater     | 

## My solution: 
```
SELECT name AS "Band name" from bands;
```

### 3. Select the Oldest Album

Make sure to only return one result from this query, and that you are not returning any albums that do not have a release year.

| id | name                   | release_year | band_id | 
|----|------------------------|--------------|---------| 
| 5  | ...And Justice for All | 1988         | 2       | 


## MY SOLUTION 
```
SELECT id, name, release_year, band_id 
FROM albums 
WHERE release_year is NOT NULL 
ORDER BY release_year 
LIMIT 1;
```

### 4. Get all Bands that have Albums


There are multiple different ways to solve this problem, but they will all involve a join.

Return the band name as `Band Name`.

| Band Name         | 
|-------------------| 
| Seventh Wonder    | 
| Metallica         | 
| The Ocean         | 
| Within Temptation | 
| Death             | 
| Van Canto         | 

## My Solution 
```
SELECT DISTINCT bands.name as "Bands Name"
FROM albums INNER JOIN bands 
ON albums.band_id = bands.id
WHERE albums is not null;
```


### 5. Get all Bands that have No Albums
[Solution](solutions/5.sql)

This is very similar to #4 but will require more than just a join.

Return the band name as `Band Name`.

| Band Name     | 
|---------------| 
| Dream Theater | 

## My Solution 
```
SELECT DISTINCT bands.name AS "Band name"
FROM bands LEFT JOIN albums 
ON bands.id = albums.band_id
WHERE albums.id IS NULL;
```

### 6. Get the Longest Album
[Solution](solutions/6.sql)

This problem sounds a lot like #3 but the solution is quite a bit different. I would recommend looking up the SUM aggregate function.

Return the album name as `Name`, the album release year as `Release Year`, and the album length as `Duration`.

| Name           | Release Year | Duration          | 
|----------------|--------------|-------------------| 
| Death Magnetic | 2008         | 74.76666593551636 | 

## My Solution: 
```
-- Remember to put albums.name instead of name because the call to name can be ambiguous. 
SELECT albums.name, release_year, SUM(songs.length) AS "duration"
FROM albums INNER JOIN songs ON albums.id=songs.album_id
GROUP BY albums.id
ORDER BY duration DESC
LIMIT 1; 
```

### 7. Update the Release Year of the Album with no Release Year
[Solution](solutions/7.sql)

Set the release year to 1986.

You may run into an error if you try to update the release year by using `release_year IS NULL` in the WHERE statement of your UPDATE. This is because MySQL Workbench by default will not let you update a table that has a primary key without using the primary key in the UPDATE statement. This is a good thing since you almost never want to update rows without using the primary key, so to get around this error make sure to use the primary key of the row you want to update in the WHERE of the UPDATE statement.

## My Solution: 
```
UPDATE albums
SET release_year=1986 WHERE release_year IS NULL AND id=4;
```

### 8. Insert a record for your favorite Band and one of their Albums
[Solution](solutions/8.sql)

If you performed this correctly you should be able to now see that band and album in your tables.

## My Solution: 
```
INSERT INTO bands (name) VALUES ('The Eagles'); 

SELECT id FROM bands WHERE name='The Eagles' //returns 8. 

INSERT INTO albums (name, release_year, band_id) 
VALUES ('One of These Nights', 1975, 8)
```

### 9. Delete the Band and Album you added in #8
[Solution](solutions/9.sql)

The order of how you delete the records is important since album has a foreign key to band.

## My Solution: 
```
-- need to delete albums first, because we don't have on Delete Cascade set, and albums has Bands as a foreign key.
SELECT id from albums where name='One of These Nights'; 
DELETE from albums WHERE id=19;

SELECT id FROM bands WHERE name='The Eagles'; 
DELETE FROM bands WHERE id=8; 
```

### 10. Get the Average Length of all Songs
[Solution](solutions/10.sql)

Return the average length as `Average Song Duration`.

| Average Song Duration | 
|-----------------------| 
| 5.352472513259112     | 

## My Solution: 
```
-- For some weird reason, in Postgres, the as alias needs to be covered with "", and colNames as ''
SELECT AVG(length) as "Average Song Duration" FROM songs; 
```


### 11. Select the longest Song off each Album
[Solution](solutions/11.sql)

Return the album name as `Album`, the album release year as `Release Year`, and the longest song length as `Duration`.

| Album                       | Release Year | Duration | 
|-----------------------------|--------------|----------| 
| Tiara                       | 2018         | 9.5      | 
| The Great Escape            | 2010         | 30.2333  | 
| Mercy Falls                 | 2008         | 9.48333  | 
| Master of Puppets           | 1986         | 8.58333  | 
| ...And Justice for All      | 1988         | 9.81667  | 
| Death Magnetic              | 2008         | 9.96667  | 
| Heliocentric                | 2010         | 7.48333  | 
| Pelagial                    | 2013         | 9.28333  | 
| Anthropocentric             | 2010         | 9.4      | 
| Resist                      | 2018         | 5.85     | 
| The Unforgiving             | 2011         | 5.66667  | 
| Enter                       | 1997         | 7.25     | 
| The Sound of Perseverance   | 1998         | 8.43333  | 
| Individual Thought Patterns | 1993         | 4.81667  | 
| Human                       | 1991         | 4.65     | 
| A Storm to Come             | 2006         | 5.21667  | 
| Break the Silence           | 2011         | 6.15     | 
| Tribe of Force              | 2010         | 8.38333  | 

## My Solution: 
```
SELECT albums.name, release_year, MAX(songs.length) as "Duration"
FROM albums JOIN songs ON albums.id = songs.album_id
GROUP BY albums.id; 
```

### 12. Get the number of Songs for each Band
[Solution](solutions/12.sql)

This is one of the toughest question on the list. It will require you to chain together two joins instead of just one.

Return the band name as `Band`, the number of songs as `Number of Songs`.

| Band              | Number of Songs | 
|-------------------|-----------------| 
| Seventh Wonder    | 35              | 
| Metallica         | 27              | 
| The Ocean         | 31              | 
| Within Temptation | 30              | 
| Death             | 27              | 
| Van Canto         | 32              | 

## My Solution: 
```
-- we need to perform two joins here, since we need to link the band to the songs
SELECT bands.name, COUNT(songs.id) as "Number of Songs" 
FROM bands INNER JOIN albums ON bands.id = albums.band_id 
INNER JOIN songs on albums.id = songs.album_id
GROUP BY bands.id; 
```
