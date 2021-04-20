# bigData-Project
This project is based on Word Count of a file using PySpark and Databricks.

# Input Source
 * The input Source file was taken from a URL which is in text format.For this project, I have taken data as 
   [TThe Project Gutenberg eBook of Power Through Prayer, by Edward Bounds](https://www.gutenberg.org/files/65115/65115-0.txt)
   
# Commands to Start this project

## Step 1:- Data Injection
   1. In first step, import all tlibraries and start fetching the data from the URL
   2. # fetching the text data from url
   3.import urllib.request 
stringInURL = "https://www.gutenberg.org/files/65115/65115-0.txt"
urllib.request.urlretrieve(stringInURL,"/tmp/power.txt")
   
