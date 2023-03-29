# Exploring Hacker News Posts

## Are you curious about what posts generate the most engagement on technology and startup forums? 

As a freelance data analyst, I recently completed a guided project that analyzed a dataset of submissions to the popular technology site, Hacker News. 

In this project, I focused on two types of user-submitted posts: Ask HN and Show HN.

Ask HN posts are submissions where the user asks the Hacker News community a specific question, while Show HN posts are submissions where the user showcases a project, product, or something interesting.

The goal was to determine which type of post received more comments on average and whether posts created at certain times of the day received more comments. 

Through my analysis, I discovered some fascinating insights that could help you understand the factors that contribute to engagement on online forums. 

With this knowledge, you could potentially improve your own online content and engagement strategies.

Check out my analysis of this Hacker News dataset to learn more!

## Reading in the data

Note: The data set we're working with was reduced from almost 300,000 rows to approximately 20,000 rows by removing all submissions that did not receive any comments, and then randomly sampling from the remaining submissions.


```python
# import modules and definitions

import pandas as pd
from csv import reader

open_file = open('hacker_news.csv')
read_file = reader(open_file)

hn = list(read_file) # read it in as a list of lists


# separating header from data

headers = hn[0]

hn = hn[1:]
```


```python
# verify seperation

print(headers)

print('\n')

print(hn[:6])
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    
    
    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01'], ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12'], ['10482257', 'Title II kills investment? Comcast and other ISPs are now spending more', 'http://arstechnica.com/business/2015/10/comcast-and-other-isps-boost-network-investment-despite-net-neutrality/', '53', '22', 'Deinos', '10/31/2015 9:48']]
    


```python
# create a pandas dataframe from the list of lists - out of curiosity

df = pd.DataFrame(hn, columns = headers)

df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>title</th>
      <th>url</th>
      <th>num_points</th>
      <th>num_comments</th>
      <th>author</th>
      <th>created_at</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12224879</td>
      <td>Interactive Dynamic Video</td>
      <td>http://www.interactivedynamicvideo.com/</td>
      <td>386</td>
      <td>52</td>
      <td>ne0phyte</td>
      <td>8/4/2016 11:52</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10975351</td>
      <td>How to Use Open Source and Shut the Fuck Up at...</td>
      <td>http://hueniverse.com/2016/01/26/how-to-use-op...</td>
      <td>39</td>
      <td>10</td>
      <td>josep2</td>
      <td>1/26/2016 19:30</td>
    </tr>
    <tr>
      <th>2</th>
      <td>11964716</td>
      <td>Florida DJs May Face Felony for April Fools' W...</td>
      <td>http://www.thewire.com/entertainment/2013/04/f...</td>
      <td>2</td>
      <td>1</td>
      <td>vezycash</td>
      <td>6/23/2016 22:20</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11919867</td>
      <td>Technology ventures: From Idea to Enterprise</td>
      <td>https://www.amazon.com/Technology-Ventures-Ent...</td>
      <td>3</td>
      <td>1</td>
      <td>hswarna</td>
      <td>6/17/2016 0:01</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10301696</td>
      <td>Note by Note: The Making of Steinway L1037 (2007)</td>
      <td>http://www.nytimes.com/2007/11/07/movies/07ste...</td>
      <td>8</td>
      <td>2</td>
      <td>walterbell</td>
      <td>9/30/2015 4:12</td>
    </tr>
  </tbody>
</table>
</div>



## Extracting Ask HN, Show HN, and other posts

We separate posts based on whether they start with Ask HN or Show HN and collect the data for those two types of posts in different lists.


```python
# collect posts into different lists

ask_posts = []
show_posts = []
other_posts = []

for row in hn: # loop through hn to separate the posts
    title = row[1]
    if title.lower().startswith('ask hn'):
        ask_posts.append(row)
    elif title.lower().startswith('show hn'):
        show_posts.append(row)
    else:
        other_posts.append(row)

# check the number of posts in each list        

print(len(ask_posts))
print(len(show_posts))
print(len(other_posts))
```

    1744
    1162
    17194
    

## Calculate which posts receive more comments


```python
# calculate the average comments per Ask HN post:

total_ask_comments = 0

for row in ask_posts: # sum the number of all comments
    comments = int(row[4])
    total_ask_comments += comments

print(total_ask_comments)
avg_ask_comments = total_ask_comments/len(ask_posts) # calculate the average number of comments

print(round(avg_ask_comments))
```

    24483
    14
    


```python
# calculate the average comments per Show HN post:

total_show_comments = 0

for row in show_posts: # sum the number of all comments
    comments = int(row[4])
    total_show_comments += comments

print(total_show_comments)
avg_show_comments = total_show_comments/len(show_posts) # calculate the average number of comments

print(round(avg_show_comments))
```

    11988
    10
    

The results of our calculations clearly indicate that Ask HN posts receive a far greater average number of comments (14) than Show HN posts (10) do.

This is not too surprising since asking question is a great way to engage people.

We'll focus our further analysis just on the Ask HN posts.

## Determine if ask posts created at a certain time are more likely to attract comments.

If we wanted to post an ask post during the hour that typically receives the most comments, we can find that hour.


```python
# calculate the number of ask posts and comments received per hour of the day

import datetime as dt

result_list = [] # will be a list of lists ([created_at time, number of comments received])

for row in ask_posts:
    created = row[6]
    comments = int(row[4])
    
    result_list.append([created, comments])

counts_by_hour = {}
comments_by_hour = {}
date_format = "%m/%d/%Y %H:%M"

for row in result_list: # create datetime objects from strings
    dt_object = dt.datetime.strptime(row[0], date_format)
    hour = dt_object.strftime("%H")
    
    if hour not in counts_by_hour: # use dictionaries to count posts and comments per hour
        counts_by_hour[hour] = 1
        comments_by_hour[hour] = row[1]
    else:
        counts_by_hour[hour] += 1
        comments_by_hour[hour] += row[1]
```


```python
print(comments_by_hour)
```

    {'09': 251, '13': 1253, '10': 793, '14': 1416, '16': 1814, '23': 543, '12': 687, '17': 1146, '15': 4477, '21': 1745, '20': 1722, '02': 1381, '18': 1439, '03': 421, '05': 464, '19': 1188, '01': 683, '22': 479, '08': 492, '04': 337, '00': 447, '06': 397, '07': 267, '11': 641}
    


```python
# calculate the average comments per hour per Ask HN post

avg_by_hour = [] # will be a list of lists ([hour, average number of comments per post])

for hour in comments_by_hour:
    avg_by_hour.append([hour, comments_by_hour[hour]/counts_by_hour[hour]])

print(avg_by_hour)
```

    [['09', 5.5777777777777775], ['13', 14.741176470588234], ['10', 13.440677966101696], ['14', 13.233644859813085], ['16', 16.796296296296298], ['23', 7.985294117647059], ['12', 9.41095890410959], ['17', 11.46], ['15', 38.5948275862069], ['21', 16.009174311926607], ['20', 21.525], ['02', 23.810344827586206], ['18', 13.20183486238532], ['03', 7.796296296296297], ['05', 10.08695652173913], ['19', 10.8], ['01', 11.383333333333333], ['22', 6.746478873239437], ['08', 10.25], ['04', 7.170212765957447], ['00', 8.127272727272727], ['06', 9.022727272727273], ['07', 7.852941176470588], ['11', 11.051724137931034]]
    

## Let's sort this list of lists and print the five highest values in a format that's easier to read.


```python
# swap columns of the avg_by_hour list

swap = []

for row in avg_by_hour:
    swap.append([row[1], row[0]])

sorted_swap = sorted(swap, reverse = True) # sort the swapped list
```


```python
print("Top 5 Hours for Ask Posts Comments")
print('\n')

for row in sorted_swap[:5]:
    hour = dt.datetime.strptime(row[1], "%H").strftime("%H:%M")
    string = "{h}: {n:.2f} average comments per post" # output format
    print(string.format(h = hour, n = row[0]))
    print('\n')
    
```

    Top 5 Hours for Ask Posts Comments
    
    
    15:00: 38.59 average comments per post
    
    
    02:00: 23.81 average comments per post
    
    
    20:00: 21.52 average comments per post
    
    
    16:00: 16.80 average comments per post
    
    
    21:00: 16.01 average comments per post
    
    
    

If we work under the hypothesis that the hour that typically receives the most comments per post is also the hour where we want to post our content, then 3 PM to 4 PM EST would be the ideal time. For me, this translates to 9 PM local time.

Posting then, we would expect to receive about 60% more comments than during the second best hour.

## That's a wrap!

Per our analysis, posting an ask post between 15:00 and 16:00 (3:00 pm est - 4:00 pm est) is expected to maximise comments received per post.

Of course, there is the caveat that I haven't used the full dataset for analysis as the note in "Reading in the data" points out.

### Here are some next steps to potentially investigate

- Determine if show or ask posts receive more points on average.
- Determine if posts created at a certain time are more likely to receive more points.
- Compare your results to the average number of comments and points other posts receive.
