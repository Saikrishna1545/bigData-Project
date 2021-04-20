# bigData-Project
This project is based on Word Count of a file using PySpark and Databricks.

## Databricks published link
https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/548800649193321/3277976591978073/7444943312366472/latest.html

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
## Step 3:- Processing the data
1.  In this step we will pair up each word in the  file andthen after we  count it as 1 as an intermediate Key-value pairs and need to transform the words using reduceByKey() to get the total count of all distinct words. To get back to python, we use collect() and then print the obtained results.
* map() words to (word,1) immediate key-value pairs
```python
powerPairsRDD = powerRemoveSpaceRDD.map(lambda eachWord: (eachWord,1))
```
* transforming the words using reduceByKey() to get (word,count) results
```pytho
powerWordCountRDD = powerPairsRDD.reduceByKey(lambda acc, value: acc + value)
```
* collect() action to get back to python
```pytho
results = powerWordCountRDD.collect()
print(results)
```
* based on highest count sorting of the word and displaying them in descending order
```pytho
sorted(results, key=lambda t: t[1], reverse=True)[:10]
```
## Charting the data
* Displaying the obtained results and  importing all the libraries to plot the graph
```pytho
import pandas as pd # 
import matplotlib.pyplot as plt
import seaborn as sns
```
* reparing chart information
```pytho
source = 'The Project Power Through Prayer by Edward M. Bounds'
title = 'Top Words in ' + source
xlabel = 'Words'
ylabel = 'Count'
df = pd.DataFrame.from_records(output, columns =[xlabel, ylabel]) 
plt.figure(figsize=(20,4))
sns.barplot(xlabel, ylabel, data=df, palette="cubehelix").set_title(title)
```
## Result 
![Output after processing the data](https://github.com/Saikrishna1545/bigData-Project/blob/main/outputresult.JPG)
![Output after Charting the data](https://github.com/Saikrishna1545/bigData-Project/blob/main/bargraph.JPG)

## To create an image of word cloud
* first We need to import "word Cloud" and "Natural Language tool kit" libraries to show the highest word count for given the input file.Then, we need to define few functions to process the data and the result will shown in figure.
```pytho
import matplotlib.pyplot as plt
import nltk
import wordcloud
from nltk.corpus import stopwords # to remove the stopwords from the data
from nltk.tokenize import word_tokenize #  breaking down the text into smaller units called tokens
from wordcloud import WordCloud # creating an image using words in the data
 
# defining the functions to process the data
class WordCloudGeneration:
    def preprocessing(self, data):
        # convert all words to lowercase
        data = [item.lower() for item in data]
        # load the stop_words of english
        stop_words = set(stopwords.words('english'))
        # concatenate all the data with spaces.
        paragraph = ' '.join(data)
        # tokenize the paragraph using the inbuilt tokenizer
        word_tokens = word_tokenize(paragraph) 
        # filter words present in stopwords list 
        preprocessed_data = ' '.join([word for word in word_tokens if not word in stop_words])
        print("\n Preprocessed Data: " ,preprocessed_data)
        return preprocessed_data
 
    def create_word_cloud(self, final_data):
        # initiate WordCloud object with parameters width, height, maximum font size and background color
        # call the generate method of WordCloud class to generate an image
        wordcloud = WordCloud(width=1600, height=800, max_font_size=200, background_color="white").generate(final_data)
        # plt the image generated by WordCloud class
        plt.figure(figsize=(12,10))
        plt.imshow(wordcloud)
        plt.axis("off")
        plt.show()
 
wordcloud_generator = WordCloudGeneration()
# you may uncomment the following line to use custom input
# input_text = input("Enter the text here: ")
import urllib.request
url = "https://www.gutenberg.org/files/65115/65115-0.txt"
request = urllib.request.Request(url)
response = urllib.request.urlopen(request)
input_text = response.read().decode('utf-8')
 
input_text1 = input_text.split('.')
clean_data = wordcloud_generator.preprocessing(input_text1)
wordcloud_generator.create_word_cloud(clean_data)
```
## Result of word Cloud
![](https://github.com/Saikrishna1545/bigData-Project/blob/main/wordcloud.JPG)

## Conclusion
Based on the results obtained, the top 15 words of 'The Project Gutenberg eBook of Power Through Prayer, by Edward Bounds' are 'prayer','god','praying', 'preacher', 'work','may', 'heart','preaching','project','men','must','man','life','gods' and 'great'.how to make donations to the Project Gutenberg Literary Archive Foundation, how to help produce our new eBooks, and how to subscribe to our email newsletter to hear about new eBooks

##  References
- [Introduction to PySpark](https://github.com/denisecase/starting-spark)
- [Spark Key Terms](https://sparkbyexamples.com/)
- [To build an image using  WordCloud](https://www.section.io/engineering-education/word-cloud/)
