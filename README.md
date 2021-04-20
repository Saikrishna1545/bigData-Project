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
2. Next, moving file from temp folder to databricks storage folder of dbfs

```python
ddbutils.fs.mv("file:/tmp/power.txt","dbfs:/data/throughPower.txt")
```
3. Transfering  the data file into Spark 
```python
powerRawRDD= sc.textFile("dbfs:/data/throughPower.txt")
````
## Step 2:- Cleaning the data
1. separating the words from each using flatmap function and changing all the words to lower case and then removing the spaces between them
```python
powerMessyTokensRDD = powerRawRDD.flatMap(lambda eachLine: eachLine.lower().strip().split(" "))
```
2.removing punctuations and importing regular expression library
```python
import re
wordsAfterCleanedTokensRDD = powerMessyTokensRDD.map(lambda letter: re.sub(r'[^A-Za-z]', '', letter))
```
3. removing all the stop words from the data using filter function
```python
from pyspark.ml.feature import StopWordsRemover
remover = StopWordsRemover()
stopwords = remover.getStopWords()
powerWordsRDD = wordsAfterCleanedTokensRDD.filter(lambda word: word not in stopwords)
```
4. removing all the empty spaces from the data
```python
powerRemoveSpaceRDD = powerWordsRDD.filter(lambda x: x != "")
```
