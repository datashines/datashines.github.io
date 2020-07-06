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
 
<pre><code class="python">
df = pd.DataFrame()
yr_range = range(1933,2022)
for yr in tqdm(yr_range):
    url = f"https://en.wikipedia.org/wiki/List_of_Bollywood_films_of_{yr}"
    website_url = requests.get(url).text
    soup = BeautifulSoup(website_url, "lxml")
    film_tables = soup.findAll("table", {"class":"wikitable"})
    film_table_dfs = pd.read_html(url, attrs={"class": "wikitable"}, header=0)
    if len(film_tables)!=len(film_table_dfs):
        film_table_dfs += pd.read_html(url, attrs={"class": "wikitable sortable"}, header=0)
    assert len(film_tables)==len(film_table_dfs), f"{len(film_tables)}, {len(film_table_dfs)}"
    for i in range(len(film_tables)):
        if film_table_dfs[i].columns is None or 'Title' not in film_table_dfs[i].columns.tolist():
            continue
        for titles in film_table_dfs[i]['Title']:
            links = film_tables[i].findAll('a', text=titles)
            for link in links:
                film_url = "https://en.wikipedia.org" + link.get('href')
                if 'redlink=1' in film_url:
                    continue
                try:
                    # song_table_list = pd.read_html(film_url, attrs={"class": "tracklist"})
                    song_table_list = pd.read_html(film_url)
                except:
                    print(f"no soundtracks found for {film_url} , skipping!")
                    continue
                for song_table in song_table_list:
                    try:
                        df_curr_to_copy = song_table[song_table.columns & ['Title', 'Singer(s)', 'Lyricist', 'Singer', 'Music',
                                                                          'Lyrics', 'Composer', 'Artist', 'Artist(s)']]
                        df_curr_to_copy['year'] = yr
                        df = pd.concat([df, df_curr_to_copy])
                    except:
                        print(f"Columns not found, available columns are --> {song_table.columns.tolist()}")
                        print(f"Relevant URL --> {film_url}")
                        continue
</code></pre>

This is what we get as our final dataframe after this step:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/dataframe.png)

Note that there are other columns we tried to extract as well, but apparently data does not exist for 
all the columns all the time. This indicates need for data cleaning which we will do next.

 
## Data Processing

From the extracted data in the previous step, we need to get the count of songs for each singer for each 
year. Because _Singer(s)_ column might contain list of names, we need to convert that string into list 
of names. Next, we need to explode the singer column such that each row has just one singer. Finally, we
assign a count of 1 to each row and then groupby (year, singer) followed by sum of counts.

<pre><code class="python">
df_singers = df_copy[df_copy['Singer(s)'].notnull()].copy()[['year', 'Singer(s)']]
df_singers['Singer(s)'] = df_singers['Singer(s)'].map(lambda c: c.split(', '))
df_singers = df_singers.explode('Singer(s)')
df_singers = df_singers.rename(columns={'Singer(s)': 'Singer'})
df_singers['count'] = 1
df_singers = df_singers.groupby(['year', 'Singer']).sum().reset_index()
df_singers = df_singers[['year', 'Singer', 'count']].sort_values(by=['year', 'count', 'Singer'], ascending=[True, False, True])
</code></pre>

This shall shape the data as something like this:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/processed_df.png)

We are all set to create the bar chart race !

## Bar Chart Race 

Using the dataframe from the step above, we will first generate a static barchart for a given year of
data. This will be a horizontal bar graph showing top 15 singers (based on count) for that year. Once
we have created the function for generating static chart for 1 year, we can use matplotlib's FuncAnimation
module to generate animated bar charts year-by-year. The function for generating static bar-chart is as follows:

<pre><code class="python">
fig, ax = plt.subplots(figsize=(50, 25))
def draw_barchart(df, year, col_name):
    dff = df[df['year'].eq(year)].sort_values(by='count', ascending=True).tail(15)
    ax.clear()
    ax.barh(dff[col_name], dff['count'], color=[colors[x] for x in dff['Singer']])
    dx = dff['count'].max()/100
    for i, (value, name) in enumerate(zip(dff['count'], dff[col_name])):
        ax.text(value-dx, i,     name, size=45, weight=570, ha='right', va='center')
        ax.text(value+dx, i,     f'{value:,.0f}',  size=50, ha='left',  va='center')
    ax.text(1, 0.4, f"{year}", transform=ax.transAxes, color='#777777', 
            size=127, ha='right', weight=700)
    ax.text(0, 1.06, 'Songs sung (approx.)', transform=ax.transAxes, size=55, color='#777777')
    ax.xaxis.set_major_formatter(ticker.StrMethodFormatter('{x:,.0f}'))
    ax.xaxis.set_ticks_position('top')
    ax.tick_params(axis='x', colors='#777777', labelsize=42)
    ax.set_yticks([])
    ax.margins(0, 0.01)
    ax.grid(which='major', axis='x', linestyle='-')
    ax.set_axisbelow(True)
    ax.text(0, 1.12, f'The most popular bollywood {col_name.lower()} from 1933 to 2021',
            transform=ax.transAxes, size=67, weight=600, ha='left')
    ax.text(1, 0, 'by @arj7192', transform=ax.transAxes, ha='right',
            color='#777777', bbox=dict(facecolor='white', alpha=0.8, edgecolor='white'), size=57)
    plt.box(False)
draw_barchart(df_singers, 2020, 'Singer')
</code></pre>

The last line in this code shall generate the graph for year 2020, as follows:

![]({{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/bar_chart_static.png)

Before animating the bar charts, we define a wrapper specific to singers over the above generic function. 
Once done, we can animate the bar charts. Firstly, the code for this section will look as follows:

<pre><code class="python">
def draw_barchart_singer(yr):
    return draw_barchart(df_singers, yr, 'Singer')
import matplotlib.animation as animation
fig, ax = plt.subplots(figsize=(50, 25))
animator = animation.FuncAnimation(fig, draw_barchart_singer, frames=range(1933, 2021))
animator.to_html5_video()
</code></pre>
 
The output animation should look something like this:
 
 <video width="1600" height="750" autoplay loop>
  <source src="{{ site.baseurl }}/data/2020-07-06-Bar-Chart-Race-Bollywood-Singers/top_bollywood_singers_race.mp4" type="video/mp4">
Your browser does not support the video tag.
</video> 

## Conclusion

This is a quick and fun project which can be extended to other entities like Lyricists, Music composers, 
etc. I have done similar work for Directors and Movie Genres, however because the data is relatively less
(a director directs far fewer number of films than the number of songs sung by a singer). However, the code
for that exercise as well as the above demonstration for singers, including other scaffolding code is 
available at my [github](https://github.com/arj7192/bollywood_singers_bar_chart_race)

## Acknowledgements

Grateful to [Wikipedia](https://en.wikipedia.org/) for being the amazing data source it is. I also took
inspiration from [this medium blog](https://medium.com/analytics-vidhya/web-scraping-wiki-tables-using-beautifulsoup-and-python-6b9ea26d8722)
 to scrape tabular data from wikipedia pages and the bar chart race code was mostly borrowed from 
 [another well-written medium blog.](https://towardsdatascience.com/bar-chart-race-in-python-with-matplotlib-8e687a5c8a41)


Thank you for reading this post. Hope it was useful !