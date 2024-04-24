# **nosql_challenge** #

--------

## **Project Goal** ##
* This project aims to analyze food ratings data provided by the UK Food Standards Agency. The analysis is commissioned by the editors of a food magazine, "Eat Safe, Love," to support their journalists and food critics in identifying establishments for future articles.

## **Analysis Examples:** ##

* Identify establishments with a perfect hygiene score (20).
* Find establishments in London with a high rating (4 or above).
* Locate the top 5 highly-rated (5) establishments closest to a newly added restaurant, considering their geographic location.
* Analyze the distribution of hygiene scores across different Local Authority areas.

--------

## **How does the code work?** ##

### **IMPORTING DATA FROM RESOURCES FOLDER (mongoimport)** ###
* The first thing that has to be done is importing the data from the resources folder called "establishments.json" either inside the jupyter lab notebook or in the terminal using `mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json` to create the 'uk_food' database and the collection "establishments"


### **NoSQL_setup_starter.ipynb** ###
1) Importing necessary dependencies ('from pymongo import MongoClient' and
'from pprint import pprint')
2) Creating an instance of MongoClient using "MongoClient(port=27017)" and then assigning that to a variable called "mongo"
3) Using the ".list_database_names()" function and then printing results to show all the databases avaliable and double checking that "uk_food" database was created from the mongoimport done above
4) Assigning the "uk_food" database to a variable called "db"
5) Using the ".list_collections_names()" function and then printing results to show all the collections in the "uk_food" database ==> The only collection that should show up would "establishments" created from the mongoimport above
6) Then a simple ".find_one()" function is used to double check that the data was imported correctly and the result is printed "pretty" using pprint()
7) The collection "establishment" from the database is then stored as a variable called "establishment" for easier query writing purposes later in the code
8) New establishment data is then inserted into the "establishment" collection for a place called "Penang Flavours" by creating a dictionary of "Penang Flavours" data and then using the ".insert_one()" function to add that data into the collection "establishments" ==> A query is then used to find "BusinessName" that is equal to "Penang Flavours" to make sure that the data did sucessfully insert into the "establishment" collection
9) A query is then created with "BusinessType" equal to "Restaurant/Cafe/Canteen" and projections as "BusinessTypeID" and "BusinessType" (Projections are set equal to 1 ==> This means that only "BusinessTypeID" and "BusinessType will be output in the results) ==> The results are then pretty printed with "pprint()" and a for loop is used to grab all results that match the query criteria
10) Once the "BusinessTypeID" is found for all "BusinessType" equal to "Restaurant/Cafe/Canteen" => The "BusinessTypeID" for "Penang Flavours" can be set to 1 (as an integer) ==> This is done by using the .update_one() function
11) Then using a '.find_one()'function and having "BusinessName" that is equal to "Penang Flavours" to check that the "BusinessTypeID" for "Penang Flavours" was sucessfully updated to 1
12) ".count_documents()" is then used to count all documents that have "Dover" as a "LocalAuthorityName" ==> ".delete_many" is then used to delete all of these documents from the "establishment" collection ==> The same ".count_documents" function is used to make sure that the count is now 0 and there are no documents left with "Dover" as the "LocalAuthorityName" ==> another ".find_one" query is used to double check that other documents remain and only "Dover" documents were deleted
13) Next, the latitude and longitude for each establisment has to be updated from string values to decimal values ==> This is accomplished by using ".update_many function" for both latitude and longitude ==> A ".find_one()" function is used to double check that the latitude and longitudes were updated correctly
14) Another ".update_many()" function is used to set all non 1-5 rating values to Null values 
15) Lastly, another ".update_many()" function is used to set all the "RatingValue" from strings to integers for all documents ==> Then a final ".find_one()" function is used to confirm that the coordinates and "RatingValues" were converting to the correct data types sucessfully

### **NoSQL_analysis_starter.ipynb** ###