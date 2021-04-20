# bigData-Project
This project is based on Word Count of a file using PySpark and Databricks.

# Input Source
 * The input Source file was taken from a URL which is in text format.For this project, I have taken data as 
   [TThe Project Gutenberg eBook of Power Through Prayer, by Edward Bounds](https://www.gutenberg.org/files/65115/65115-0.txt)
   
# Commands to Start this project

## Step 1:- Data Injection
   1. In first step, import all tlibraries and start fetching the data from the URL
   ```python
# fetching the text data from url
import urllib.request 
stringInURL = "https://www.gutenberg.org/files/65115/65115-0.txt"
urllib.request.urlretrieve(stringInURL,"/tmp/power.txt")
```
   2. Next, moved the file from temp folder to databricks storage folder of dbfs
    ```python
  dbutils.fs.mv("file:/tmp/power.txt","dbfs:/data/throughPower.txt")
    ```
    3. Transferring the data file into spark
    ```python
    powerRawRDD= sc.textFile("dbfs:/data/throughPower.txt")
    ```
