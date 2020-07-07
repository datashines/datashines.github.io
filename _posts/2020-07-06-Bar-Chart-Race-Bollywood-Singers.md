---
layout: post
title: Bar Chart Race - Bollywood Singers
---

Bar chart races have lately become popular as a form of visualization. This post is all about
generating one from scratch including an interesting data extraction process. We 
will generate the bar chart race for the most popular singers Bollywood has had between the years 1933 
and 2020. We will directly parse data from Wikipedia. Using nested loops, we will first iterate 
through the pages of films of a given year and then skim through each film page for a given year. We 
will extract data using beautifulsoup and pandas and use matplotlib animation to generate the bar chart 
race.

## Data Extraction

We use wikipedia as our source of data. Essentially we traverse through the list of films for each year 
and in the list, we further go to each film's page. On the film page, we look for the songs list (or the
soundtrack table. We do so for each film for each year from 1933 to 2020. For example, here is the wiki 
page for list of films for 1980:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/list_of_films.png)

And here is the page of one film from this list of films, which also shows the soundtrack table:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/film_page.png)

As we can see, the films are listed on a table, under the column _Title_. Using BeautifulSoup library in 
Python, we extract all the tables of this page i.e. elements with <table> tag, and for each table, we 
look for hyperlinks under the _Title_ column. We enter these hyperlinks (i.e. film pages) one by one, 
and using pandas library of Python, we read tables on these pages that have columns Singer(s) or Singer 
in them. The relevant code snippet is as follows:
 
{% gist 9e7baa591aefaed7ec11c47f8b8453a5 %}

This is what we get as our final dataframe after this step:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/dataframe.png)

Note that there are other columns we tried to extract as well, but apparently data does not exist for 
all the columns all the time. This indicates need for data cleaning which we will do next.

 
## Data Processing

From the extracted data in the previous step, we need to get the count of songs for each singer for each 
year. Because _Singer(s)_ column might contain list of names, we need to convert that string into list 
of names. Next, we need to explode the singer column such that each row has just one singer. Finally, we
assign a count of 1 to each row and then groupby (year, singer) followed by sum of counts.

{% gist 155245a964e5411d754146d19b1f5c0a %}

This shall shape the data as something like this:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/processed_df.png)

We are all set to create the bar chart race !

## Bar Chart Race 

Using the dataframe from the step above, we will first generate a static barchart for a given year of
data. This will be a horizontal bar graph showing top 15 singers (based on count) for that year. Once
we have created the function for generating static chart for 1 year, we can use matplotlib's FuncAnimation
module to generate animated bar charts year-by-year. The function for generating static bar-chart is as follows:

{% gist f2ca089abc1efe1af3dc1ce7e810c9fb %}

The last line in this code shall generate the graph for year 2020, as follows:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/bar_chart_static.png)

Before animating the bar charts, we define a wrapper specific to singers over the above generic function. 
Once done, we can animate the bar charts. Firstly, the code for this section will look as follows:

{% gist b43bd51198f40964e10bba3725090e70 %}
 
The output animation should look something like this:
 
 <video width="512" height="256" autoplay loop>
  <source src="{{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/top_bollywood_singers_race.mp4" type="video/mp4">
Your browser does not support the video tag.
</video> 

## Conclusion

This is a quick and fun project which can be extended to other entities like Lyricists, Music composers, 
etc. I have done similar work for Directors and Movie Genres, however because the data is relatively less
(a director directs far fewer number of films than the number of songs sung by a singer). The code
for that exercise as well as the above demonstration for singers, including other scaffolding code is 
available at my [github](https://github.com/arj7192/bollywood_singers_bar_chart_race)

## Acknowledgements

Grateful to [Wikipedia](https://en.wikipedia.org/) for being the amazing data source it is. I also took
inspiration from [this medium blog](https://medium.com/analytics-vidhya/web-scraping-wiki-tables-using-beautifulsoup-and-python-6b9ea26d8722)
 to scrape tabular data from wikipedia pages and the bar chart race code was mostly borrowed from 
 [another well-written medium blog.](https://towardsdatascience.com/bar-chart-race-in-python-with-matplotlib-8e687a5c8a41)


Thank you for reading this post. Hope it was useful !