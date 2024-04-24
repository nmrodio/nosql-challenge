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
1) Importing necessary dependencies ('from pymongo import MongoClient', 'from pprint import pprint', and 'import pandas as pd')
2) Creating an instance of MongoClient using "MongoClient(port=27017)" and then assigning that to a variable called "mongo"
3) Assigning the "uk_food" database to a variable called "db"
4) Using the ".list_collections_names()" function and then printing results to show all the collections in the "uk_food" database ==> The only collection that should show up would "establishments" created from the mongoimport above
5) The collection "establishment" from the database is then stored as a variable called "establishment" for easier query writing purposes later in the code
6) The first analysis that was done was to find "Which establishments have a hygiene score equal to 20?" ==> This was done by creating a query that has "scores.Hygiene" equal to 20 ==> Then using the ".count_documents" function to find the number of documents that fit the query criteria and this code is saved as a variable called "hygiene_count" ==> ".find()" is then used and the query is passed into the function and the results are turned into a list using "list()" and this is saved as a variable called "hygiene_results" ==> Finally a f string is used to print out a sentence stating the number of documents that fit the query criteria using "hygiene_count" and a for loop is used to pprint the results ([:1] only prints out the first results from the list of outputs)
7) A dataframe is then created from "hygiene_results" using "pd.json_normalize" instead of "pd.DataFrame" ("pd.json_normalize" splits up the nested dictionaries inside the list of dictionaries from the results into individual columns) ==> An f string is then used to print out a sentence stating the number of rows in the dataframe using 'len' function ===> Finally the first 10 rows of the DataFrame are displayed
8) The second analysis that was done was to find "Which establishments in London have a'RatingValue' greater than or equal to 4?" ==> This was done by creating a query that has "LocalAuthorityName" equal to 'London' ('$regex' is used here to grab all documents that have "London" any where in the "LocalAuthorityName") AND a "RatingValue" that is greater than or equal to 4==> Then using the ".count_documents" function to find the number of documents that fit the query criteria and this code is saved as a variable called "lond_rating_counts" ==> ".find()" is then used and the query is passed into the function and the results are turned into a list using "list()" and this is saved as a variable called "lond_rating_results" ==> Finally a f string is used to print out a sentence stating the number of documents that fit the query criteria using "lond_rating_counts" and a for loop is used to pprint the results ([:1] only prints out the first results from the list of outputs)
9) A dataframe is then created from "lond_rating_results" using "pd.json_normalize" instead of "pd.DataFrame" ("pd.json_normalize" splits up the nested dictionaries inside the list of dictionaries from the results into individual columns) ==> An f string is then used to print out a sentence stating the number of rows in the dataframe using 'len' function ===> Finally the first 10 rows of the DataFrame are displayed
10) The third analysis that was done was to find "What are the top 5 establishments with a 'RatingValue' rating value of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?" ==> This was done by defining variables: 'degree_search = 0.01', 
'latitude = 51.49014200', and 'longitude = 0.08384000' (This is the lat and long of "Pengag Flavours") ==> Next a query is created to search within 0.01 degree on either side of the latitude and longitude and establishments that have a 'RatingValue' of 5 ==> Next a sort variable is created to sort the results by hygiene score 'sort = [{'scores.Hygiene', 1}]' (1 is used to specify ascending order) and a limit variable is also defined and set equal to 5 for the top 5 results from the query ==> All three of these components are put together into a variable called "top_five_results" and the results are converted into a list and a for loop is used to pprint the five results.
11) A dataframe is then created from "top_five_results" using "pd.json_normalize" instead of "pd.DataFrame" ("pd.json_normalize" splits up the nested dictionaries inside the list of dictionaries from the results into individual columns) ==> The dataframe is then displayed to show the top five results
12) The fourth and finally analysis that was done was to find "How many establishments in each Local Authority area have a hygiene score of 0?" ==> This was accomplished by creating a 'match_query', 'group_query', and 'sort_query' and then passing those queries into a pipeline with a variable called "pipeline" ==> The 'match_query' matches establishments with a hygiene score of 0, the 'group_query' groups the matches by Local Authority and then counts each establishment in each Local Authority with a hygiene score of 0, and the 'sort_query' sorts the counts of each Local Authority in descending order (Highest to Lowest) ==> The pipeline variable is then passed into the ".aggregate()" function and then the results are converting into a list and stored as a variable called "pipeline_results" ===> An f string is then used to print out a sentence stating the number of documents found in the "pipeline_results" and a for loop is used to pprint the results from the pipeline query ([:10] is used to only return the first 10 results from "pipeline_results")
13) A finally dataframe is then created from "pipeline_results" using "pd.DataFrame" ("pd.json_normalize" is not needed for this query because there arent any nested dictionaries inside the list of dictionaries from the 'pipeline_results' that need to be split up into individual columns) ==> An f string is then used to print out a sentence stating the number of rows in the dataframe using 'len' function ===> Finally the first 10 rows of the DataFrame are displayed

