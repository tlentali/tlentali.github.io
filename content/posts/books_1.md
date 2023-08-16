+++ 
draft = false
date = 2022-08-15T18:33:12+02:00
title = "Books"
description = "Know your books."
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = ['Books']
+++

Did you ever wonder how many book surround you ?  
No ? Because I did.

But how boring it is to type the title of each books...  
Thankfully, each book have a barcode.  
And the barcode is the International Standard Book Number of the book, and it is unique.  

So... I bought this :

![](https://www.cdiscount.com/pdt2/1/3/5/2/400x400/AUT6785129719135.jpg)

It took me about 10 minutes to scan all my books. It is amazing.  

We have a list of ISBN numbers :  

```text
isbn
9782707181978
9782020321266
9782290381403
9781682450505
```

The goal now is to retrieve some info on the book, let's read our data :  

```python
with open('../data/book/books_isbn.csv') as f:
    isbn = f.read().splitlines()
```

Sample of isbn :  

```text
['9782707181978',
 '9782020321266',
 '9782290381403',
 '9781682450505']
```

We can start to use `request` on the google api to get each book information :  

```python
import requests
import time


books = {}
cmp = 0
tot_books = len(isbn)

for book in tqdm(isbn):
    try:
        books[book] = requests.get(f'https://www.googleapis.com/books/v1/volumes?q=isbn:{book}').json()
    except:
        books[book] = 'no isbn'
    time.sleep(0.5)
```

Here we build the url by inserting the isbn into it. The tqdm allow us to follow the progression of the scrapping.  

Then, we extract the json info into a Pandas DataFrame : 

```python 
def return_on_failure(value):
    def decorate(f):
        def applicator(*args, **kwargs):
            try:
                return f(*args,**kwargs)
            except:
                return value
        return applicator
    return decorate

@return_on_failure('')
def get_title(books, book):
    return books[book]['items'][0]['volumeInfo']['title']

@return_on_failure([])
def get_author(books, book):
    return books[book]['items'][0]['volumeInfo']['authors']

@return_on_failure(0)
def get_page_count(books, book):
    return books[book]['items'][0]['volumeInfo']['pageCount']
    
@return_on_failure('')
def get_language(books, book):
    return books[book]['items'][0]['volumeInfo']['language']
    
@return_on_failure([])
def get_categorie(books, book):
        return books[book]['items'][0]['volumeInfo']['categories']

df = {}
for book in isbn:
    df[book] = {}
    df[book]['title'] = get_title(books, book)
    df[book]['authors'] = get_author(books, book)
    df[book]['page_count'] = get_page_count(books, book)
    df[book]['language'] = get_language(books, book)
    df[book]['categories'] = get_categorie(books, book)

df_result = pd.DataFrame.from_dict(df).T
df_result = df_result.rename_axis('isbn').reset_index()
df_result = df_result[['isbn', 'title', 'authors', 'page_count', 'language', 'categories']]
```

We used a decorator function to control missing data. 

Output : 

```text
|    |          isbn | title                        | authors                                  |   page_count | language   | categories     | cover   |   keep |
|---:|--------------:|:-----------------------------|:-----------------------------------------|-------------:|:-----------|:---------------|:--------|-------:|
|  0 | 9782707181978 | Eloge du carburateur         | ['Matthew B Crawford']                   |          249 | fr         | ['philosophy'] | False   |      1 |
|  1 | 9781682450505 | The Elephants in My Backyard | ['Rajiv Surendra']                       |          145 | en         | ['story']      | True    |      1 |
|  2 | 9782290381403 | Flatland                     | ['Edwin A. Abbott']                      |          128 | fr         | ['philosophy'] | False   |      1 |
```
