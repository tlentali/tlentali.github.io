# knosis


##### Table of Contents  
- [Headers](#headers)  

- data flux
  - [Init dataframe](#init_dataframe)  
  - [Read csv](#read_csv)  
  - [Write to csv](#write_to_csv)  
  - [Hive query](#hive_query)  
  - [Format pandas encoding](#format_pandas_encoding)  

- data structure
  - [Rename column](#rename_column)  
  - [Rename column](#rename_column)  
  - [Split one column into several lines or columns](#split)  
  - [Remove columns and keep other](#remove_col)  
  - [Pivot](#pivot)  
  - [Column string to datetime](#str_dt)  
  - [Table croisé dynamique](#cross_dyn)  

- analysis
  - [Find value in a column](#find_value_in_a_column)  
  - [Convert](#convert)  
  - [Instr, contains](#Instr_contains)  
  - [Change the value of index](#change_the_value_of_index)  
  - [To string without decimal](#to_string_without_decimal)  
  - [Na naan none](#na_naan_none)  
  - [Sort values](#sort)
  - [Serie to dataframe](#seriedataframe)  
  - [Get the max value from a group by](#maxrow)  
  - [Shap model explainer](#shap)

- operation
  - [JOIN](#join)  
  - [If statement](#if_statement)  
  - [GROUP BY, numeroter, rank](#numeroter_rank_group_by)  
  - [Normalization](#normalization)  
  - [Function](#function)  
  - [Drop duplicates if reverse is present between two columns](#droprev)  
  - [Generate list of date](#dt)  
  - [Normalise group by](#norm_groupby)  
  - [Function in pandas](#pandas_funct)  

- visualisation
  - [Plot histogram from pandas](#plot_histogram_from_pandas)  
  - [Add pluggin in dataframe](#pluggin)  
  - [Visu data pandas profiling](#pandas_profiling)  
  - [Color by row](#colorrow)  
  - [Graph from pandas](#graph_pandas)  
  - [Cufflinks : Plotly in pandas](#cufflinks)
  - [Fit hist curve](#fit_hist_curve)
  - [Show in jupyter](#show)  
  - [Save graph](#save_graph)  
  - [Display full dataframe in Notebook](#display_full_dataframe)  
  - [Display styled dataframe in Notebook](#display_styled_dataframe)  
  - [Display barplot with percent](#display_barplot_percent)  
  - [Stack plot with or without percent](#stack_percent)  


## Header <a name="headers"/>
```python
# coding: utf-8
```

# Data flux

## Init dataframe <a name="init_dataframe"/>
```python
import pandas as pd
import numpy as np

df = pd.DataFrame(np.random.randint(0,100,size=(100, 4)), columns=list('ABCD'))
```
[[Source]](https://stackoverflow.com/questions/32752292/how-to-create-a-dataframe-of-random-integers-with-pandas)


```python
df = pd.DataFrame({'A': [1, 0, 0, 0, 0], 
                   'B': [0, 1, 0, 0, 0], 
                   'C': [0, 0, 1, 0, 0], 
                   'D': [0, 0, 0, 1, 0], 
                   'E': [0, 0, 0, 0, 1], 
                   'r_rank': [1, 2, 3, 4, 5]}, 
                  index = [0, 1, 2, 3, 4]) 
```


## Read csv <a name="read_csv"/>
```python
df = pd.read_csv('../data_file/dqtq.csv', 
                 sep = ';', 
                 encoding='latin1')
```


## Write to csv <a name="write_to_csv"/>
```python
df.to_csv('data.csv', 
          sep=';', 
          index=False)
```


## Hive query <a name="hive_query"/>
```python
import pandas as pd
import pypyodbc

query = """
select
   *
from
   database
where
   dt >= date_sub(current_date, 1)
"""

conn = pypyodbc.connect(connectString='DSN=speech; CHARSET=UTF8', autocommit=True)
df = pd.read_sql (query, conn)
conn.close()
```


## Format pandas encoding <a name="format_pandas_encoding"/>
```python
encoding='utf-8'
encoding='cp1252'
encoding='latin1'
```


# Data structure

## Rename columns <a name="rename_column"/>
- All columns :  
```python
df.columns = ['new_col_name_a', 'new_col_name_b']
```

- Specific columns :  
```python
df = df.rename(columns={'old_name_1': 'new_name_1', 
                        'old_name_2': 'new_name_2'})
```


## Split one column into several columns or lines <a name="split"/>
```python
df_2 = pd.DataFrame(df_1['col'].str.split(','))
df_3 = pd.DataFrame([x for x in df_2["col"]])
df_3.columns = ['col_1', 'col_2', 'col_3', 'cal_4'...]
```

```python
df = pd.DataFrame([["a", "e f g h"],["b", "toto tata"]], columns= ["col1", "sentence"])
# separation into columns
df.sentence.str.split(' ', expand=True)
# separation into row
df.sentence.str.split(' ', expand=True).stack() 
```


## Remove columns and keep other <a name="remove_col"/>
```python
df = df[list(set(df).difference({'col1toremove', 'col2toremove'}))]
```


## Pivot <a name="pivot"/>

```python
df_pivote = df.pivot_table(columns='col1', index='col2')

# remove multilevel index :
df_pivote_score = df_pivote.xs('col3', axis=1, drop_level=True)#.reset_index(drop=True)
```
[[Source]](https://stackoverflow.com/questions/22233488/pandas-drop-a-level-from-a-multi-level-column-index)



## Column string to datetime <a name="str_dt"/>

```python
df['dt'] = pd.to_datetime(df['dt'], format="%Y-%m-%d")
```
[[Source]](https://stackoverflow.com/questions/17134716/convert-dataframe-column-type-from-string-to-datetime)

## Table croisé dynamique <a name="cross_dyn"/>

```python
df = pd.DataFrame(dict(A=['A','A','A','A','A','B','B','B','B'],
                       B=[1,1,1,2,2,1,1,1,2],
                       C=['CA','NY','CA','FL','FL',     
                          'WA','FL','NY','WA']))
tbl = df[['A', 'B', 'C']].drop_duplicates()\
                         .groupby(['A','B'])['C']\
                         .apply(list)
```

[[Source](https://towardsdatascience.com/10-python-pandas-tricks-to-make-data-analysis-more-enjoyable-cb8f55af8c30)]


# Analysis

## Find value in a column <a name="find_value_in_a_column"/>
```python
df[df['col_name'] == "mot"]
```
or 
```python
df.loc[df['col_name'] == "mot"]
```


## Convert <a name="convert"/>
- To float :  
```python
df.convert_objects(convert_numeric=True)
```
[\[source\]](https://stackoverflow.com/questions/16729483/converting-strings-to-floats-in-a-dataframe)

- To string :  
```python
df['col'] = df['col'].astype(str)
```


## Instr, contains <a name="Instr_contains"/>
```python
df[df['col'].str.contains('mot')]
```


## Change the value of index from 'Republic of Korea' to 'South Korea' <a name="change_the_value_of_index"/>
```python
as_list = df.index.tolist()
idx = as_list.index('Republic of Korea')
as_list[idx] = 'South Korea'
df.index = as_list
```
Basically, you get the index as a list, change that one element, and the replace the existing index.  
[[Source]](https://stackoverflow.com/questions/40427943/how-do-i-change-a-single-index-value-in-pandas-dataframe)


## To string without decimal <a name="to_string_without_decimal"/>
```python
df['col'] = df['col'].map(lambda x: '{:.0f}'.format(x)).astype(str)
```

Also :
```python
df.astype(float).sum().map(lambda x: '{:.0f}'.format(x))
```
[[Source]](https://stackoverflow.com/questions/38516316/pandas-converting-floats-to-strings-without-decimals)


## Na naan none <a name="na_naan_none"/>
- Drop na :  
```python
df = df.dropna(axis=0, how='any')
```
[[Source]](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.dropna.html)

- Fill na with 0
```python
df['col'] = df['col'].fillna(0)
```



## Sort value <a name="sort"/>
```python
df.sort_values(by=['col1', 'col2'], ascending=[True, False])
```


## Serie to dataframe <a name="seriedataframe"/>
```python
serie.to_frame()
```

The hard way :  
```python
pd.DataFrame({'var':serie.index, 'cluster': serie.values})
```


## Get the max value from a group by <a name="maxrow"/>
```python
In [1]: df
Out[1]:
    Sp  Mt Value  count
0  MM1  S1     a      3
1  MM1  S1     n      2
2  MM1  S3    cb      5
3  MM2  S3    mk      8
4  MM2  S4    bg     10
5  MM2  S4   dgd      1
6  MM4  S2    rd      2
7  MM4  S2    cb      2
8  MM4  S2   uyi      7

In [3]: idx = df.groupby(['Mt'])['count'].transform(max) == df['count']

In [4]: df[idx]
Out[4]:
    Sp  Mt Value  count
0  MM1  S1     a      3
3  MM2  S3    mk      8
4  MM2  S4    bg     10
8  MM4  S2   uyi      7
```
[[Source]](https://stackoverflow.com/questions/15705630/python-getting-the-row-which-has-the-max-value-in-groups-using-groupby)


# Shap model explainer <a name="shap"/>

```python
import shap

# load JS visualization code to notebook
shap.initjs()

# train XGBoost model

# explain the model's predictions using SHAP values
# (same syntax works for LightGBM, CatBoost, and scikit-learn models)
explainer = shap.TreeExplainer(clf)
shap_values = explainer.shap_values(customer_to_explain)

# visualize the first prediction's explanation (use matplotlib=True to avoid Javascript)
for i in range(6):
    #print('segment_' + str(i+1))
    display(shap.force_plot(explainer.expected_value[0], 
                            shap_values[i], 
                            feature_names=keeped_features, 
                            matplotlib=False, 
                            out_names=['segment ' + str(i+1)]
                           ))
    
# Display all features and SHAP values
df1=pd.DataFrame(data=shap_values[0], columns=X.columns, index=[0])
df2=pd.DataFrame(data=shap_values[1], columns=X.columns, index=[1])
df3=pd.DataFrame(data=shap_values[2], columns=X.columns, index=[2])
df4=pd.DataFrame(data=shap_values[3], columns=X.columns, index=[3])
df5=pd.DataFrame(data=shap_values[4], columns=X.columns, index=[4])
df6=pd.DataFrame(data=shap_values[5], columns=X.columns, index=[5])
df=pd.concat([df1,df2,df3,df4,df5,df6])
display(df.transpose())

# Display sum of SHAP values for each segment (https://evgenypogorelov.com/multiclass-xgb-shap.html)
df.transpose().sum()
```

[[Source](https://shap.readthedocs.io/en/latest/)]
[[Source](https://gust.dev/python/model-interpretability)]
[[Source](https://evgenypogorelov.com/multiclass-xgb-shap.html)]

# Operation

## JOIN <a name="join"/>
- Quick :  
```python
df = pd.merge(df1,
              df2,
              how='left',
              left_on=['id_key'],
              right_on=['fk_key'])
              
df.head()
```

- Complet :  
```python
df = pd.merge(df1,
              df2,
              how='left',
              left_on=['id_key',
                       'id_key_2'],
              right_on=['fk_key',
                        'id_key_2_other_name_ok'],
              suffixes=['_name_df1',
                        '_name_df2'])
```


## If statement <a name="if_statement"/>
```python
def the_function(row):
   if row['col_a'] == 'A':
       return 'a'
   elif row['col_b'] == 'B':
       return 'b'
   else:
       return 'c'

df = df.assign(new_col = df.apply(the_function, axis=1))
```
[[Source]](https://stackoverflow.com/questions/19913659/pandas-conditional-creation-of-a-series-dataframe-column)

or use np.where, faster:

```python
import pandas as pd
import numpy as np

EU = ["Austria","Belgium","Germany"]

df1 = pd.DataFrame(data={"Country":["USA","Germany","Russia","Poland"], "Capital":["Washington","Berlin","Moscow","Warsaw"]})
df1["EU"] = np.where(df1["Country"].isin(EU), "EU", "Other")
```

[[Source](https://stackoverflow.com/questions/45184549/python-pandas-new-columns-value-if-the-item-is-in-the-list)]

or use .loc:  
```python
from pandas import DataFrame

Numbers = {'set_of_numbers': [1,2,3,4,5,6,7,8,9,10]}
df = DataFrame(Numbers,columns=['set_of_numbers'])

df.loc[df.set_of_numbers <= 4, 'equal_or_lower_than_4?'] = 'True' 
df.loc[df.set_of_numbers > 4, 'equal_or_lower_than_4?'] = 'False' 

print (df)
```

[[Source](https://datatofish.com/if-condition-in-pandas-dataframe/)]


## GROUP BY, numeroter, rank <a name="numeroter_rank_group_by"/>
```python
df['auction_rank'] = df.groupby(['auction_ID'])['bid_price'].rank(ascending=False)
```
[[Source]](https://stackoverflow.com/questions/30425796/pandas-rank-by-column-value)

```python
df['name_2'] = df['name_1'].astype(float)
df_grouped = df.groupby('name_3')['name_to_mean'].mean().reset_index()
```

- List value grouped :  
```python
df_mab_search_id = df_mabaya.groupby('sku')['search_id'].apply(list).reset_index()
```


## Normalization <a name="normalization"/>
```python
def norm(df, col_to_norm):
    return df[col_to_norm]/df[col_to_norm].loc[df[col_to_norm]
                                                   .abs()
                                                   .idxmax()].astype(np.float64)
```

To norm two cols and generate a stacked graph :  
```python
def norm2cols(df, col1, col2):
    col1_normed = str(col1) + '_normed'
    col2_normed = str(col2) + '_normed'
    df[col1_normed] = df[col1] *100 / (df[col1] + df[col2])
    df[col2_normed] = df[col2] *100 / (df[col1] + df[col2])


norm2cols(df, '0', '1')

# to plot the stacked graph :
df[['0_normed', '1_normed']].sort_values(ascending=False, by='1_normed').plot(kind='bar', stacked=True, figsize=[20, 10])
```


## Function <a name="function"/>
```python
def cleanning(row):
    row = row.replace('  ', ' ')
    return row

df["name_clean"] = df["name"].apply(lambda x: cleanning(str(x)))
```


## Generate list of date <a name="dt"/>
```python
import datetime as dt
from datetime import datetime

def list_date(date_origine, nb_date):
    base = datetime.strptime(date_origine, "%Y-%m-%d")
    return [(base + dt.timedelta(days=x)).strftime("%Y-%m-%d") for x in range(0, nb_date)]

list_date('2018-10-14', 4)
```

- Generate list of double date :  
```python
def list_double_date(date_origine, nb_date):
    base = datetime.strptime(date_origine, "%Y-%m-%d")
    return [[(base + dt.timedelta(days=x-1)).strftime("%Y-%m-%d"), (base + dt.timedelta(days=x)).strftime("%Y-%m-%d")] for x in range(0, nb_date)]

list_double_date('2018-10-14', 4)
```


## Function in pandas <a name="pandas_funct"/>
```python
import pandas as pd
import numpy as np

def thomas_lentalisé(méchants):
    return np.random.choice(['POUF', 'PAF PAF', 'ET CHBAM', 'URAKEN', 'ET LAWWWW'])

pd.Series.thomas_lentalisé = thomas_lentalisé

méchants = [
    'COLONEL HANS LANDA', 'LE JOKER', 'DARK VADOR', 'LA TEAM ROCKET', 
    'RAMSAY BOLTON', 'CAPITAINE CROCHET', 'TERMINATOR'
]
data = {'gros_méchant': méchants}
df = pd.DataFrame(data)

for gros_méchant in df.gros_méchant:
    print(gros_méchant, df.thomas_lentalisé(), sep=' \U00002620 ')
```
[[Source]](https://github.com/santosjorge/cufflinks/blob/e2accbf980f77c7b9e539e14d9312bd0ff5b9edb/cufflinks/plotlytools.py#L1718)



## Normalise group by <a name="norm_groupby"/>
```python
df2 = df.groupby(['subset_product', 'subset_close']).size().reset_index(name='prod_count')
a = df2.groupby('subset_product')['prod_count'].transform('sum')
df2['prod_count'] = df2['prod_count'].div(a)
```
[[Source]](https://stackoverflow.com/questions/49105734/pandas-get-normalize-values-from-groupby-and-size)


## Drop duplicates if reverse is present between two columns <a name="droprev"/>

I have a dataset with 2 columns like the following :
```
InteractorA InteractorB
AGAP028204  AGAP005846
AGAP028204  AGAP003428
AGAP028200  AGAP011124
AGAP028200  AGAP004335
AGAP028200  AGAP011356
AGAP028194  AGAP008414
```
I'm using Pandas and I want to drop rows which are present twice but simply reversed like the following:  
from this:
```
InteractorA InteractorB
AGAP002741  AGAP008026
AGAP008026  AGAP002741
```
To this:
```
InteractorA InteractorB
AGAP002741  AGAP008026
```
Solution :
```python
In [8]: df
Out[8]:
  InteractorA InteractorB
0  AGAP028204  AGAP005846
1  AGAP028204  AGAP003428
2  AGAP028200  AGAP011124
3  AGAP028200  AGAP004335
4  AGAP028200  AGAP011356
5  AGAP028194  AGAP008414
6  AGAP002741  AGAP008026
7  AGAP008026  AGAP002741

In [18]: df['check_string'] = df.apply(lambda row: ''.join(sorted([row['InteractorA'], row['InteractorB']])), axis=1)

In [19]: df
Out[19]:
  InteractorA InteractorB          check_string
0  AGAP028204  AGAP005846  AGAP005846AGAP028204
1  AGAP028204  AGAP003428  AGAP003428AGAP028204
2  AGAP028200  AGAP011124  AGAP011124AGAP028200
3  AGAP028200  AGAP004335  AGAP004335AGAP028200
4  AGAP028200  AGAP011356  AGAP011356AGAP028200
5  AGAP028194  AGAP008414  AGAP008414AGAP028194
6  AGAP002741  AGAP008026  AGAP002741AGAP008026
7  AGAP008026  AGAP002741  AGAP002741AGAP008026

In [20]: df.drop_duplicates('check_string')
Out[20]:
  InteractorA InteractorB          check_string
0  AGAP028204  AGAP005846  AGAP005846AGAP028204
1  AGAP028204  AGAP003428  AGAP003428AGAP028204
2  AGAP028200  AGAP011124  AGAP011124AGAP028200
3  AGAP028200  AGAP004335  AGAP004335AGAP028200
4  AGAP028200  AGAP011356  AGAP011356AGAP028200
5  AGAP028194  AGAP008414  AGAP008414AGAP028194
6  AGAP002741  AGAP008026  AGAP002741AGAP008026
shareedit
```
[[Source]](https://stackoverflow.com/questions/24676705/pandas-drop-duplicates-if-reverse-is-present-between-two-columns/24680568)


# Visualisation

## Plot histogram from pandas <a name="plot_histogram_from_pandas"/>
- Graph :  
```python
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib
matplotlib.style.use('ggplot')
%matplotlib inline

df = pd.read_csv('file', sep=' ')
df['column'].hist(bins=75, figsize=(15, 10), color='royalblue', alpha = 0.5)
```

```python
import pandas as pd
import pypyodbc
import matplotlib.pyplot as plt
import matplotlib
matplotlib.style.use('ggplot')
%matplotlib inline

df['col'].value_counts()

df['col'].value_counts().plot(kind='bar', 
                              title="Distribution', 
                              figsize=(15, 10), 
                              legend=True, 
                              fontsize=12, 
                              color='royalblue', 
                              alpha = 0.5)
```

- Value :  
```python
df['col'].value_counts(bins=200, sort=False)[1000:10000]
```

- Avec graph :  
```python
df['col'].value_counts(bins=200, sort=False)[1000:10000].plot()
```


## Fit hist curve <a name="fit_hist_curve"/>
```python
from scipy import stats
import numpy as np
import matplotlib.pylab as plt

# create some normal random noisy data
ser = df_3['Confidence'].loc[(df_3['Confidence']>1000) & (df_3['Confidence']<10000)]

# plot normed histogram
plt.hist(ser, normed=True,bins=75, color='royalblue', alpha = 0.5)

# find minimum and maximum of xticks, so we know
# where we should compute theoretical distribution
xt = plt.xticks()[0]
xmin, xmax = min(xt), max(xt)
lnspc = np.linspace(xmin, xmax, len(ser))


# lets try the normal distribution first
m, s = stats.norm.fit(ser) # get mean and standard deviation
pdf_g = stats.norm.pdf(lnspc, m, s) # now get theoretical values in our interval
plt.plot(lnspc, pdf_g, label="Norm") # plot it

# exactly same as above
ag,bg,cg = stats.gamma.fit(ser)
pdf_gamma = stats.gamma.pdf(lnspc, ag, bg,cg)
plt.plot(lnspc, pdf_gamma, label="Gamma")

# guess what :)
ab,bb,cb,db = stats.beta.fit(ser)
pdf_beta = stats.beta.pdf(lnspc, ab, bb,cb, db)
plt.plot(lnspc, pdf_beta, label='Beta')

plt.show()
```


## Show in jupyter <a name="show"/>
```python
show()
```
or :  
```python
display()
```


## Add pluggin in dataframe <a name="pluggin"/>
```python
import ipywidgets as widgets
from ipywidgets import interactive

# “All” option allows us to display all the rows
items = ['All']+sorted(df_requete_clean['cat'].tolist())
items2 = ['All']+sorted(df_requete_clean['dt'].tolist())

    
def view(x='', y=''):
    if x=='All' and y=='All': return df_requete_clean
    elif x=='All' and y!='All': return df_requete_clean[df_requete_clean['dt']==y]
    elif x!='All' and y=='All': return df_requete_clean[df_requete_clean['cat']==x]
    else:
        return df_requete_clean[(df_requete_clean['cat']==x) & (df_requete_clean['dt']==y)]
 
w = widgets.Select(options=items)
v = widgets.Select(options=items2)
interactive(view, x=w, y=v)
```


## Visu data pandas profiling <a name="pandas_profiling"/>
Install:  
```python
pip install pandas-profiling
```

Example:   
```python
import pandas as pd
import pandas_profiling

df = pd.read_csv('file_name', sep=';')
pandas_profiling.ProfileReport(df)
```
[[Source](https://github.com/pandas-profiling/pandas-profiling)]


## Degradé de couleur par ligne <a name="colorrow"/>
```python
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib import colors

def background_gradient(row):
    cmap='OrRd'
    m=row.min()
    M=row.max()
    low=0
    high=0.2
    rng = M - m
    norm = colors.Normalize(m - (rng * low),
                            M + (rng * high))
    normed = norm(list(row))
    c = [colors.rgb2hex(x) for x in plt.cm.get_cmap(cmap)(normed)]
    return ['background-color: %s' % color for color in c]

mk.T.style.apply(background_gradient, axis=1)
```


## Graph from pandas <a name="graph_pandas"/>
```python
import matplotlib.pyplot as plt
import pandas as pd

fig = plt.figure()

ax=df.groupby(['image_name'])['index'].nunique().hist(bins=50)
ax = fig.add_subplot(ax)

plt.ylabel('nb_entries', fontsize=20)
plt.xlabel('nb_detected_objects_per_image', fontsize=20)
plt.rcParams["figure.figsize"] = (15, 10)
plt.title('Nb detected object per image', fontsize=30)
fig.savefig('nb_detected_obj_per_image.png', dpi=fig.dpi)
```


## [Cufflinks](https://plot.ly/ipython-notebooks/cufflinks/#cufflinks-reference) : plotly in pandas <a name="cufflinks"/>

### Basic
```python
import cufflinks as cf
import pandas as pd

cf.go_offline()

df = cf.datagen.lines()
df.iplot(kind='scatter')
```
[[Source]](https://plot.ly/ipython-notebooks/cufflinks/)

It works directly in jupyter notebook.  
To make it works in jupyterlab, write in a terminal:  
```
$ jupyter labextension install @jupyterlab/plotly-extension
```
[[Source]](https://stackoverflow.com/questions/44439771/using-plot-ly-in-jupyterlab-graphics-does-not-show/45993029#45993029)

### To url (as an app):  
```python
df.iplot(kind='scatter', filename='cf-simple-line', theme='ggplot', asUrl=True)
```

This will generate an html file.  
Then to display the html file:
```bash
python -m http.server 8823
```
and go to ```http://machine.machin.biz:8823``` and select the html file.

### To image
in iplot, add :
```python
asImage=True
```

### Add annotations
```python
annotations = [dict(x='2015-03-01',
                    y=5,
                    xref='x',
                    yref='y',
                    text='dict Text',
                    showarrow=True,
                    arrowhead=7,
                    ax=0,
                    ay=-40)]
df.iplot(kind='scatter', filename='cf-simple-line', theme='ggplot', annotations=annotations)                    
```

Automatic annotation on each max:  
```python
annotations = [dict(x=df[value].idxmax(),
                    y=df[value].max(),
                    xref='x',
                    yref='y',
                    text='max of ' + str(value),
                    textangle = 0,
                    showarrow=True,
                    arrowhead=6,
                    arrowcolor='#ff7f0e',
                    ax=40,
                    ay=-40) for value in df.columns
              ]

df.iplot(kind='scatter', filename='cf-simple-line', theme='ggplot', annotations=annotations)
```

Might help:  
```python
# give position of the value index
df['col1'].index.get_loc(value)
```
```python
# if value is index
x=value,
y=df['col1'].loc[value]
```

### Several plot on same output
Into a cell in jupyter, just put the iplots in the same cell and execute it, it will diplay all the iplots as output:
```python
import cufflinks as cf
import pandas as pd
import numpy as np
import matplotlib
cf.go_offline()

df1.iplot(kind='scatter', filename='cf-simple-line1', theme='ggplot')
df2.iplot(kind='scatter', filename='cf-simple-line2', theme='ggplot')
```

If you want to generate an unique html containing several plots use :
```python
import cufflinks as cf
import pandas as pd
import numpy as np
import matplotlib
cf.go_offline()

df1 = cf.datagen.lines()
df2 = cf.datagen.lines()
df3 = cf.datagen.lines()

f1 = df1.iplot(kind='scatter', filename='cf-simple-line1', theme='ggplot', asFigure=True)
f2 = df2.iplot(kind='scatter', filename='cf-simple-line2', theme='ggplot', asFigure=True)
f3 = df3.iplot(kind='scatter', filename='cf-simple-line2', theme='ggplot', asFigure=True)

cf.iplot(cf.subplots([f1, f2, f3],
                     shape=(3,1),
                     shared_xaxes=True,
                     subplot_titles=['the first', 'the second', 'the third']))

# to generate the html file, uncomment this:
#cf.iplot(cf.subplots([f1, f2, f3],
#                     shape=(3,1),
#                     shared_xaxes=True,
#                     subplot_titles=['the first', 'the second', 'the third']), 
#         filename='cf-multiple-line1',
#         asUrl=True)
```

### Change x axis range
```python
import plotly.graph_objs as go  

layout = go.Layout(
        xaxis=dict(
            range=[-0.5, 10.5]
        )
    )
test['score'].iplot(kind='histogram', layout=layout)
```

[[Source](https://stackoverflow.com/questions/36898483/modify-axes-range-using-plotly)]

## Save graph <a name="save_graph"/>
```python
import matplotlib.pyplot as plt

df.plot(kind='bar', stacked=True, figsize=[20, 10])
figure = plt.gcf()            
figure.savefig('path/figure.png', 
               bbox_inches="tight")
```


## Display full dataframe in Notebook <a name="display_full_dataframe"/>
```python
import pandas as pd

pd.set_option('display.max_column',None)
pd.set_option('display.max_rows',None)
pd.set_option('display.max_seq_items',None)
pd.set_option('display.max_colwidth', 500)
pd.set_option('expand_frame_repr', True)
pd.set_option('display.width', 1000)
```


## Display styled dataframe <a name="display_styled_dataframe"/>

### Colors range on all values
```python
import seaborn as sns

cm = sns.light_palette("green", as_cmap=True)
df_data.groupby('kmeans_cluster').mean().T.style.background_gradient(cmap=cm)
```
[[Source]](https://pandas.pydata.org/pandas-docs/stable/user_guide/style.html)

### Colors range on each rows
```python
def background_gradient(row):
    cmap = 'YlGnBu'
    m = row.min()
    M = row.max()
    low = 0
    high = 0.2
    rng = M - m
    norm = colors.Normalize(m - (rng * low), M + (rng * high))
    normed = norm(list(row))
    c = [colors.rgb2hex(x) for x in plt.cm.get_cmap(cmap)(normed)]
    return ['background-color: %s' % color for color in c]
    
df_data.groupby('col_1').mean().T.style.apply(background_gradient, axis=1)
```

## Display barplot with percent <a name="display_barplot_percent"/>

```python
def segment_barplot(df_grouped, title):
    print(df_grouped)

    ax = df_grouped.plot(kind='bar')
    ax.set_title(title, fontsize=18)
    # create a list to collect the plt.patches data
    totals = []

    # find the values and append to list
    for i in df['seg'].unique():
        totals.append(df_grouped[i])

    # set individual bar lables using above list
    total = sum(totals)

    # set individual bar lables using above list
    for i in ax.patches:
        # get_x pulls left or right; get_height pushes up or down
        ax.text(i.get_x()+.1, i.get_height(), \
                str(round((i.get_height()/df_grouped.sum())*100, 2))+'%', fontsize=15,
                    color='dimgrey')
                    
segment_barplot(df.groupby('seg')['customer_key'].nunique(), 'Customer repartition by segment')
```
# to class as excel sf

```python
df_table = df.groupby(['area_name', 'kind'])['name'].apply(list).unstack(0)
```

# Stack plot with or without percent <a name="stack_percent"/>

```python
def stacked_plot(df, title, horizon, percentage):
    seg_color = {
        "very high": "forestgreen",
        "high": "mediumseagreen",
        "medium": "gold",
        "low": "darkorange",
        "very low": "chocolate",
        "negative": "firebrick",
    }

    color_plot = list(reversed(list(seg_color.values())))
    seg_name = (list(seg_color.keys()))

    ax = df.plot(kind='bar', stacked=True, color = color_plot, label = seg_name, rot=0)
    
    if percentage:
        for p in ax.patches:
            width, height = p.get_width(), p.get_height()
            x, y = p.get_xy() 
            ax.text(x+width/2, 
                    y+height/2, 
                    '{:.0f} %'.format(height), 
                    horizontalalignment='center', 
                    verticalalignment='center', fontsize=18)

    markers = [plt.Line2D([0, 0], [0, 0], color=color, marker='o', linestyle='') for color in
                   list(reversed(color_plot))]
    plt.legend(markers, seg_name, numpoints=1, loc='upper right', bbox_to_anchor=(1.2,1), frameon=False)
    plt.title('{}\n{} months'.format(title.upper(), horizon))
    plt.box(False)   

df_mean_glo = pd.DataFrame({'1': 14.193330, 
                   '2': 35.806375, 
                   '3': 31.814341, 
                   '4': 15.002137, 
                   '5': 2.890709, 
                   '6': 0.293109}, 
                  index = ['segment']) 

stacked_plot(df_mean_glo, 'mean', 12, percentage=True)
```
