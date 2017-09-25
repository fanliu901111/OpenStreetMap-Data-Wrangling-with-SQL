# OpenStreetMap-Data-Wrangling-with-SQL

Name:
Fan Liu
Map Area:
Seattle, WA, United States
https://www.openstreetmap.org/relation/237385
https://mapzen.com/data/metro-extracts/
I went to University of Wahington in Seattle area, so I am more intereted in this area that what database query reveals.
Data Audit
After downloading the data file from openstreetmap, we can see that it is an .osm file.The size of the original file is over 1.8G, so I used a sample_file to do the analysis first and then apply it on the whole file. Also I found out that it uses different types of tags. So, I parse the seattle_washington dataset using ElementTree and count number of the unique tags. mapparser.ipynb is used to count the numbers of unique tags.
'member': 5720
'nd': 590959
'node': 528035
'osm': 1
'relation': 654
'tag': 298167
'way': 52401
Patterns in the Tags
I created 3 regular expressions to check for certain patterns in the tags by using tag.ipynp. These are four categories with each tag:
'lower': 156611
'lower_colon': 136899
'other': 4657
'problemchars': 0
Problems Encountered in the Map
After parsing the sample size of the seattle_map area, the main problem we encountered is the street name inconsistencies. I noticed a few problems with the data:
Inconsistency of street types (Street vs. st. vs. street, S vs. South)
“Incorrect” postal codes(V8P 3Z9)
Inconsistency of city names ('Seattle' vs. 'seattle')
Data Cleaning
Using audit.ipynp, we can updated the variables to the right version. After auditing the test data file, we found some problems as stated above. And then we are going to use data.ipynp to convert the .osm file to 5 .csv flies which create a SQL database for further analysis.
The five .csv files are:
nodes.csv
nodes_tags.csv
ways.csv
ways_nodes.csv
ways_tags.csv
Data Overview and Additional Ideas
This section contains basic statistics about the dataset, the SQL queries used to gather them, and some additional ideas about the data in context.
File Sizes
ahmedabad_india.osm: 109.2 MB
nodes_csv: 42.7 MB
nodes_tags.csv: 674.8 MB
ways_csv: 47.8 MB
ways_nodes.csv: 209.9 MB
ways_tags.csv: 121.8 MB
seattle_washington.db: 1.26 GB
Number of nodes
SELECT COUNT(*) FROM nodes;
Output
7920523
Number of ways
SELECT COUNT(*) FROM ways;
Output
786012
Number of unique users
SELECT COUNT(DISTINCT(e.uid))          
FROM (SELECT uid FROM nodes UNION ALL SELECT uid FROM ways) e;
Output
3542
Top 10 contributing users
SELECT e.user, COUNT(*) as num
FROM (SELECT user FROM nodes UNION ALL SELECT user FROM ways) e
GROUP BY e.user
ORDER BY num DESC
LIMIT 10;
Output
Glassman :1297284
SeattleImport: 736303
tylerritchie: 657040
woodpeck_fixbot: 576079
alester: 364024
Omnific: 336659
Glassman_Import: 225911
CarniLvr79: 211409
STBrenden: 203418
Brad Meteor: 176167
Number of users appearing only once
SELECT COUNT(*) 
FROM
    (SELECT e.user, COUNT(*) as num
     FROM (SELECT user FROM nodes UNION ALL SELECT user FROM ways) e
     GROUP BY e.user
     HAVING num=1)  u;
Output
669
Additional Ideas
Contributor statistics and gamification suggestion
By observing the top 10 contributers, we can see that the users of contributions are slightly skewed. Here are some user percentage statistics:
Top user contribution percentage (“Glassman”) 27.12%
Combined top 2 users' contribution (“Glassman” and “SeattleImport”) 42.51%
Additional Data Exploration
Top 10 appearing amenities
SELECT value, COUNT(*) as num
FROM nodes_tags
WHERE key='amenity'
GROUP BY value
ORDER BY num DESC
LIMIT 10;
Output
bicycle_parking:3384
bench:3187
restaurant:2858
waste_basket:1463
cafe:1401
fast_food:1209
school:855
parking:806
toilets:753
place_of_worship:738
Biggest religion
SELECT nodes_tags.value, COUNT(*) as num
FROM nodes_tags 
    JOIN (SELECT DISTINCT(id) FROM nodes_tags WHERE value='place_of_worship') i
    ON nodes_tags.id=i.id
WHERE nodes_tags.key='religion'
GROUP BY nodes_tags.value
ORDER BY num DESC
LIMIT 1;
Output
christian|673
Most popular cuisines
SELECT nodes_tags.value, COUNT(*) as num
FROM nodes_tags 
    JOIN (SELECT DISTINCT(id) FROM nodes_tags WHERE value='restaurant') i
    ON nodes_tags.id=i.id
WHERE nodes_tags.key='cuisine'
GROUP BY nodes_tags.value
ORDER BY num DESC
Limit 10;
Output
mexican:    238
pizza:      230
american:   148
thai:       145
chinese:    144
asian:      138
japanese:   135
italian:    104
vietnamese:  82
burger:      72
Conclusion
The OpenStreetMap data of seattle_washington an .osm file but with typo mistakes that caused by human. I did the data clean by using audit.ipynp to updating variables. But, there are lots of improvement needed in the dataset. I think this data can be better if it contains more cirminal statistics in the Maps. On the other hand, in the future data importing process, if typos can be controlled would be so much better.
In [ ]:
