# Project 3: OpenStreetMap Data Wrangling with SQL

**Name:** Raju Singh

**Map Area**: I have choosen Mathura because it is my hometown and it is also popularly known as the birth-place of the lord Krishna.

* Location: Mathura, India
* [OpenStreetMap URL](https://www.openstreetmap.org/export#map=11/27.4957/77.6856)
* [MapZen URL](https://mapzen.com/data/metro-extracts/your-extracts/b17dfe034e34)

# 1. Data Audit
###Unique Tags
Looking at the XML file, I found that it uses different types of tags. So, I parse the Mathura,India dataset using ElementTree and count number of the unique tags.
`mapparser.py` is used to count the numbers of unique tags.

* 'bounds': 1
* 'member': 101
* 'nd': 12679
* 'node': 9645
* 'osm': 1
* 'relation': 18
* 'tag': 3272
* 'way': 1792

### Patterns in the Tags
The `"k"` value of each tag contain different patterns. Using `tags.py`, I created  3 regular expressions to check for certain patterns in the tags.
I have counted each of four tag categories.

*  `"lower" : 3156`, for tags that contain only lowercase letters and are valid,
*  `"lower_colon" : 115`, for otherwise valid tags with a colon in their names,
*  `"problemchars" : 0`, for tags with problematic characters, and
*  `"other" : 1`, for other tags that do not fall into the other three categories.

# 2. Problems Encountered in the Map
###Street address inconsistencies
The main problem we encountered in the dataset is the street name inconsistencies. Below is the old name corrected with the better name. Using `audit.py`, we updated the names.
 

* **Abbreviations** 
    * `Rd -> Road`
* **LowerCase**
    * `priti vihar colony => Priti Vihar Colony'
* **Misspelling**
    * `socity -> Society`
* **Hindi names**
    * `rasta -> Road`
* **UpperCase Words**
    * `sbk -> SBK`


#3. Data Overview
the csv files are obtained using ahmedabad_sample.osm because processing the ahmedabad_india.osm is taking too long.

### File sizes:
* `mathura.osm: 2.05 MB`
* `mathura_sample.osm 68.7 KB `
* `nodes_csv: 781 KB
* `nodes_tags.csv: 23.5 KB`
* `ways_csv: 107 KB`
* `ways_nodes.csv: 310 KB`
* `ways_tags.csv: 93.3 KB`
* `mathura.db: 1.46MB`

###Number of nodes:
``` python
sqlite> SELECT COUNT(*) FROM nodes
```
**Output:**
```
9645```

### Number of ways:
```python
sqlite> SELECT COUNT(*) FROM ways
```
**Output:**
```
1792
```

###Number of unique users:
```python
sqlite> SELECT COUNT(DISTINCT(e.uid))          
FROM (SELECT uid FROM nodes UNION ALL SELECT uid FROM ways) e;
```
**Output:**
```
55
```

###Top contributing users:
```python
sqlite> SELECT e.user, COUNT(*) as num
FROM (SELECT user FROM nodes UNION ALL SELECT user FROM ways) e
GROUP BY e.user
ORDER BY num DESC
LIMIT 10;
```
**Output:**
```
charishma	4846
lamaur	1287
Oberaffe	863
veter27	812
jaimemd	794
marek kleciak	780
andrewsh	737
NoelB	562
katpatuka	162
Narayani Barve	120

```

###Number of users contributing only once:
```python
sqlite> SELECT COUNT(*) 
FROM
    (SELECT e.user, COUNT(*) as num
     FROM (SELECT user FROM nodes UNION ALL SELECT user FROM ways) e
     GROUP BY e.user
     HAVING num=1) u;
```
**Output:**
```
13
```

# 4. Additional Data Exploration
###Common ammenities:
```python
sqlite> SELECT value, COUNT(*) as num
FROM nodes_tags
WHERE key='amenity'
GROUP BY value
ORDER BY num DESC
LIMIT 10;
```
**Output:**
```
place_of_worship	20
atm	7
bank	6
drinking_water	3
fuel	2
hospital	2
toilets	2
bus_station	1
clock	1
dentist	1

```

###Biggest religion:
```python
sqlite> SELECT nodes_tags.value, COUNT(*) as num
FROM nodes_tags 
    JOIN (SELECT DISTINCT(id) FROM nodes_tags WHERE value='place_of_worship') i
    ON nodes_tags.id=i.id
WHERE nodes_tags.key='religion'
GROUP BY nodes_tags.value
ORDER BY num DESC
LIMIT 1;
```
**Output:**
```
Hindu :	1
```
###Popular cuisines
```python
sqlite> SELECT nodes_tags.value, COUNT(*) as num
FROM nodes_tags 
    JOIN (SELECT DISTINCT(id) FROM nodes_tags WHERE value='restaurant') i
    ON nodes_tags.id=i.id
WHERE nodes_tags.key='cuisine'
GROUP BY nodes_tags.value
ORDER BY num DESC;
```
**Output:**
```
indian 1
```

# 5. Conclusion
The OpenStreetMap data of Ahmedabad is of fairly reasonable quality but the typo errors caused by the human inputs are significant. We have cleaned a significant amount of the data which is required for this project. But, there are lots of improvement needed in the dataset. The dataset contains very less amount of additional information such as amenities, tourist attractions, popular places and other useful interest. The dataset contains very old information which is now incomparable to that of Google Maps or Bing Maps.

So, I think there are several opportunities for cleaning and validation of the data in the future. 

### Additional Suggestion and Ideas

#### Control typo errors
* We can build parser which parse every word input by the users.
* We can make some rules or patterns to input data which users follow everytime to input their data. This will also restrict users input in their native language.
* We can develope script or bot to clean the data regularly or certain period.

#### More information
* The tourists or even the city people search map to see the basic amenities provided in the city or what are the popular places and attractions in the city or near outside the city. So, the users must be motivated to also provide these informations in the map.
* If we can provide these informations then there are more chances to increase views on the map because many people directly enter the famous name on the map.

# Files
* `README.md` : this file
*'mathura.osm' : data of the osm file
* `mathura_sample.osm`: sample data of the OSM file
* `audit.py` : audit street, city and update their names
* `data.py` : build CSV files from OSM and also parse, clean and shape data
* `database.py` : create database of the CSV files
* `mapparser.py` : find unique tags in the data
* `query.py` : different queries about the database using SQL
* `sample.py` : extract sample data from the OSM file
* `tags.py` : count multiple patterns in the tags

