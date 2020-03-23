# AB Test with `le AB`

> "*Life is a sum of all our choices.*"  
> **Albert Camus**

Get ready to make a few decision.

Taking decision trough AB testing has never been more popular on Internet based company.  
Even if each test is unique, two main questions seem to be asked again and again :

- is A more successful than B ?
- is A generate more than B ?

Strong statistical knowledge are required to handle it from beginning to end correctly.  
To answer in a simple and robust way those questions, we built `le AB` !  
`Le AB` is a Python library to analyse AB tests inspired by the free online tools developed by [Evan Miller](https://www.evanmiller.org/).

## Before launching ...

Before starting a test, we need to prepare the field.

- can you measure the modification ?
- how much might you expect in term of improvement ?
- do you have enough traffic or time to do the test ?

Those are very important questions you need to answer before launching the test to be sure to run it properly.  
Knowing how to measure the test and expecting some uplift inform you on the volume needed to reach significant conclusion.  
To do so, we can use the `leSample` to get a volume of sample needed if we expect a minimum detectable effect of 2% on a baseline conversion rate of 20% :

```python
from leab import leSample


ab_test = leSample(conversion_rate=20,
                   min_detectable_effect=2)
ab_test.get_size()

>>>6347
```

So you shouldn't stop your test until reaching this volume of 6347 test exposition to be sure to provide significant conclusion.  
If you know the daily visit number that reach the concerned page, you can fire up `leSample` to get a number of day :

```python
ab_test.get_duration(avg_daily_total_visitor=1000)

>>>13
```

Here, knowing that we have 1000 visits on the particular tested page per day, we need 13 days to run our Ab test.  

If you think that the sample size is realistic, let's launch the test !  
See you when you reach the needed volume !  

## ... when you reached the sample size

Great, you reached the appropriate volume computed earlier !
Let's have a look at our results :

Is it a success rating test ?

Imagine that you are running a AB test on an email campagne :
A version is the classic one used each year by the company
B get a new message
You want to know which one lead to a click on the link at the end of the mail.
In other word, which one improve the click rate success.

|          | Success | trial |
|--------- | ------- | ----- |
| sample A | 14      | 100   |
| sample B | 20      | 100   |


If so, we can draw the `leSuccess` from `le AB` like so :

```python
from leSuccess import leSuccess


ab_test = leSuccess(data.A,
                    data.B,
                    confidence_level=0.95)
ab_test.get_verdict()

>>>'No significant difference'
```

Great ! We are confident in our test that `B` is definitely more successful than `A`, well done !

Or is it a mean comparison test ?

Imagine now that you change the machine learning algo behind you search engine relevancy.  
Counting the number of click here is irrelevant, you want to know which one improve your business favorite KPI.

| A    | B    |
|------|------|
| 64.2 | 45.0 |
| 28.4 | 29.5 |
| 85.3 | 32.3 |
| 83.1 | 49.3 |
| 13.4 | 18.3 |
| 56.8 | 34.2 |
| 44.2 | 43.9 |
| 90.0 | 13.8 |
|      | 27.4 |
|      | 43.4 |

Then we pull off the `leMean` and dive :

```python
from leMean import leMean


ab_test = leMean(data.A, data.B)
ab_test.get_verdict()

>>>'Sample A mean is greater'
```

Perfect, `A` generate more that `B`, so we will keep `A` !

Now, there is only to launch (or let) the best version in prod and enjoy the improvement, or the clear non improvement, thanks to your test !

Hope it helps !
