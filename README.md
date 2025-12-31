# Exploratory Data Analysis About Video Game Sales

The purpose of the project is to identify the most successful console platforms, sales by years, performances of developer/publisher studios, correlation between genre - sales, and critic scores - sales to give an insight into developers, publishers, and stakeholders about the video game industry.

## Dataset Overview
In this project, I utilized a dataset titled “Video Game Sales,” published by Gregory Smith in 2016 on Kaggle. Here’s the source for the called dataset:

[Kaggle – Video Game Sales](https://www.kaggle.com/datasets/gregorut/videogamesales)

The dataset contains 64017 rows and 12 columns. Columns contain title, console platform, genre, publisher, developer, critic score, total sales, sales in certain regions(NA, JP, EU), sales in other regions, and release dates of games. *All sales values are expressed in millions.*

### Cleaning the Data

I’ve decided to use Excel Power Query to clean the dataset. There were nulls in the developer and total sales columns. I’ve deleted them to make the dataset suitable for my analysis. I’ve also fixed an error on the publisher column; there were date-formatted cells on it. I’ve also edited out (mil) of column names to make writing queries easier. Other than these, there were no issues with data types, blanks, columns, etc. I’ve also added an index column in case I need it in the future.

<img width="354" height="188" alt="image" src="https://github.com/user-attachments/assets/bd9082e6-4c92-4429-9c68-d91677d8d346" />

As my cleaning ended, the new dataset has 17568 rows. After the cleanup, I’ve decided to switch to PostgreSQL for analyses.

<img width="1100" height="514" alt="image" src="https://github.com/user-attachments/assets/bd77c028-5c7b-4ba1-9b03-0a72f72f4ebe" />

## Analyses of the Video Game Industry
### Comparison of Total Sales by Consoles

I start my analysis with a comparison of video game consoles by their total sales.

```
SELECT
  console,
  COUNT(DISTINCT(title)),
  ROUND(CAST(AVG(total_sales) AS NUMERIC),2) AS avg_sales_per_game,
  ROUND(CAST(SUM(total_sales) AS NUMERIC),2) AS total_sales
FROM vg_sales
GROUP BY console
ORDER BY SUM(total_sales) DESC
LIMIT 10;
```
I’ve learned the ROUND() function does not work with double precision values when using decimal points, so I had to use CAST() to convert them to numeric values.

Here are the Top 10 video game consoles by their total sales:

<img width="497" height="287" alt="image" src="https://github.com/user-attachments/assets/78443e71-f2b3-462f-a4bc-81bc224b01f6" />

PlayStation 2 takes the first place as it’s the best-selling video game console of all time. As the headline consoles of the 7th generation, Xbox 360 and PlayStation 3 complete the top 3. Xbox 360, PlayStation 3, and PlayStation 4 perform very well in average sales per game, but a lack of games compared to the PlayStation 2 blocks them from taking the first place.

### Comparison of Total Sales by Years

It’s crucial to identify if there’s a trend in the video game industry, positive or negative. That’s why this analysis is vital, especially for stakeholders and potential investors.

```
SELECT
  EXTRACT(YEAR FROM release_date) AS years,
  COUNT(DISTINCT(title)),
  ROUND(CAST(AVG(total_sales) AS NUMERIC),2) AS avg_sales_per_game,
  ROUND(CAST(SUM(total_sales) AS NUMERIC),2) AS total_sales
FROM vg_sales
GROUP BY EXTRACT(YEAR FROM release_date)
ORDER BY SUM(total_sales) DESC
LIMIT 10;
```
My original plan was to add an extra column for years just for this analysis, but EXTRACT() came in very practical, so I decided to go with it. I’m using DISTINCT() because the dataset lists games’ different platform versions.

Here are the Top 10 years by their total sales:

<img width="415" height="289" alt="image" src="https://github.com/user-attachments/assets/ffc09f8e-c0fa-405e-bea0-87af63de3dc5" />

Post-cleanup dataset covers the years between 1977 and 2020. I have to mention that the large majority of recently released games do not announce their sales numbers immediately. That’s why games released after 2015 do not appear in the list as much as others. Still, there’s a clear sign that after the 2000s, the video game industry grew as video games began to shift to the mainstream. The industry had its golden years at the end of the 2000s. There’s a clear growth as it nears the mid-2010s based on average sales per game.

### Comparison of Developers by Total Sales

Developer studios are the core of the video game industry. Some are long-established and deliver games year on year. Some are new and try to deliver innovative games to achieve success.

```
SELECT
  developer,
  COUNT(DISTINCT(title)),
  ROUND(CAST(AVG(total_sales) AS NUMERIC),2) avg_sales_per_game,
  ROUND(CAST(SUM(total_sales) AS NUMERIC),2) total_sales
FROM vg_sales
GROUP BY developer
ORDER BY SUM(total_sales) DESC
LIMIT 10;
```

Here are the Top 10 developers by their total sales:

<img width="495" height="291" alt="image" src="https://github.com/user-attachments/assets/96794cb3-2c9a-4ea9-a60c-961166b444e1" />

EA Canada(nowadays called EA Vancouver) is known for sports game franchises like FIFA, NHL, and UFC. EA Tiburon (nowadays called EA Orlando) is known for sports game franchises like Madden NFL, PGA Tour, and NBA Live. Ubisoft Montreal is known for open-world action game franchises like Assassin’s Creed, Far Cry, and Watch Dogs.

By the look of this list, sports and action game developers dominate the industry. Developers who appeal to casual gamers are also successful by their total sales. Rockstar North and Infinity Ward crush the chart with their average sales per game, thanks to the success of the Grand Theft Auto and Call of Duty franchises. Konami and Capcom show up as the working horses of the video game industry.

### Comparison of Publishers by Total Sales

Publishers finance developers to make sure their dreams come to reality. They are not obligatory for every game, as indie games are on the rise since the mid-2010s, but publishers still hold an important value for game development.

```
SELECT
  publisher,
  COUNT(DISTINCT(title)),
  ROUND(CAST(AVG(total_sales) AS NUMERIC),2) avg_sales_per_game,
  ROUND(CAST(SUM(total_sales) AS NUMERIC),2) total_sales
FROM vg_sales
GROUP BY publisher
ORDER BY SUM(total_sales) DESC
LIMIT 10;
```

Here are the Top 10 publishers by their total sales:

<img width="519" height="286" alt="image" src="https://github.com/user-attachments/assets/cb776c77-1631-4696-8407-63a9e527040a" />

Activision is known for Call of Duty, Crash Bandicoot, and Tony Hawk franchises. Electronic Arts’ sales mostly come from FIFA and Star Wars franchises. EA Sports is one of Electronic Arts’ divisions that specializes in sports games. Rockstar Games shines with its average sales per game performance.

### Comparison of Genres by Total Sales

Genres are a great way to identify trends, so stakeholders and investors use them a lot before making decisions.

```
SELECT
  genre,
  COUNT(DISTINCT(title)),
  ROUND(CAST(AVG(total_sales) AS NUMERIC),2) avg_sales_per_game,
  ROUND(CAST(SUM(total_sales) AS NUMERIC),2) total_sales
FROM vg_sales
GROUP BY genre
ORDER BY SUM(total_sales) DESC
LIMIT 10;
```

Here are the Top 10 genres by their total sales:

<img width="496" height="288" alt="image" src="https://github.com/user-attachments/assets/b6abf56f-38bd-4c3e-a722-06f32c6f5d87" />

As I have mentioned above in other analyses, sports, action, and shooter genres dominate the video game industry compared to other genres. Also, there’s a big gap between the shooter genre and others in terms of average sales per game. That’s why nowadays we see so many games of these genres as they are favorites of stakeholders and investors.

### Comparison of Developers/Publishers by Critic Scores

Even though there are no clear analyses to pull from these, it may be useful when stakeholders and investors have to decide where to invest if they want to focus on developers/publishers’ success. I’ve put a constraint of equal to or more than 5 games with critic scores to avoid any outlier performances.

```
SELECT
  developer,
  COUNT(DISTINCT(title)),
  ROUND(CAST(AVG(critic_score) AS NUMERIC),2) avg_critic_score
FROM vg_sales
GROUP BY developer
HAVING COUNT(DISTINCT(title)) >= 5 AND AVG(critic_score) IS NOT NULL
ORDER BY AVG(critic_score) DESC
LIMIT 10;
```
<img width="384" height="287" alt="image" src="https://github.com/user-attachments/assets/29fc928f-199a-4eb2-a937-d426e40be0fd" />

```
SELECT
  publisher,
  COUNT(DISTINCT(title)),
  ROUND(CAST(AVG(critic_score) AS NUMERIC),2) avg_critic_score
FROM vg_sales
GROUP BY publisher
HAVING COUNT(DISTINCT(title)) >= 5 AND AVG(critic_score) IS NOT NULL
ORDER BY AVG(critic_score) DESC
LIMIT 10;
```
<img width="381" height="286" alt="image" src="https://github.com/user-attachments/assets/85a758f5-b731-4c5d-9833-8317566038fc" />

### Comparison of Sales by Critic Scores

It’s always intriguing to see if critic-level success translates into profit. Even though most casual gamers do not pay attention to critic scores when selecting a game to play, it’s still important to analyse if there’s a correlation.

```
SELECT
 CASE
  WHEN critic_score >= 9 THEN '9-10'
  WHEN critic_score >= 8 THEN '8-9'
  WHEN critic_score >= 7 THEN '7-8'
  WHEN critic_score >= 6 THEN '6-7'
  WHEN critic_score >= 5 THEN '5-6'
  ELSE '>5'
 END critic_score_bins,
 COUNT(*),
 ROUND(CAST(SUM(total_sales) AS NUMERIC),2) AS total_sales,
 ROUND(CAST(AVG(total_sales) AS NUMERIC),2) AS avg_sales
FROM vg_sales
WHERE critic_score IS NOT NULL
GROUP BY critic_score_bins
ORDER BY critic_score_bins DESC;
```

<img width="400" height="188" alt="image" src="https://github.com/user-attachments/assets/62ad93fd-3f9d-4bcc-9e31-e35c88aafb15" />

As seen, the quantity of games with the average critic score of 7–8 and 8–9 surpasses every other category. That’s why they are at the top of total sales. But if we focus on average sales, there’s a clear correlation between success and sales numbers. Games with a critic score above 8 sell a lot more than others. On the other hand, mediocre and bad games do not sell much.

### Market Analysis for the Video Game Industry

It’s crucial to identify what is happening to the video game industry since globalization to understand where revenue is coming from. That’s why I decided to use the data to compare regions based on sales.

```
SELECT
  EXTRACT(YEAR FROM release_date) AS year,
  ROUND(CAST(SUM(na_sales) / SUM(total_sales) AS NUMERIC)*100,2) AS na_sales_perc,
  COALESCE(ROUND(CAST(SUM(jp_sales) / SUM(total_sales) AS NUMERIC)*100,2),0) AS jp_sales_perc,
  ROUND(CAST(SUM(eu_sales) / SUM(total_sales) AS NUMERIC)*100,2) AS eu_sales_perc,
  ROUND(CAST(SUM(other_sales) / SUM(total_sales) AS NUMERIC)*100,2) AS other_sales_perc 
FROM vg_sales
GROUP BY EXTRACT(YEAR FROM release_date)
HAVING COUNT(*) > 100 AND SUM(total_sales) != 0
ORDER BY EXTRACT(YEAR FROM release_date) DESC
```

I’ll break down the code step by step, as it may require some explanations. I had to use COALESCE() for jp_sales_perc because there were some years with null values. I also had to use HAVING COUNT(*) because I wanted to avoid small sample bias for some years.

<img width="562" height="638" alt="image" src="https://github.com/user-attachments/assets/4dc3aeca-50f3-4add-a7f7-0df851e4f8aa" />

As seen in the image, a significant portion of video game sales was coming from Japan before the 2000s. That’s because Japan has a rooted culture when it comes to video games. Contrary to this, video games were degraded in the West. Since the 2000s, video games have become mainstream in the West. As a result, the percentage of sales for North America jumped. As years get closer to the 2020s, North America's sales percentage gets distributed to Europe and Other sales categories because now it’s much easier to access games thanks to globalization and digitalization.

## Key Insights

The video game industry keeps growing. Nowadays, video games are one of the biggest and most popular forms of entertainment.

Many experienced developer/publisher studios participate in the industry. Some work on a single project for 5–10 years to come up with masterpiece games, some expand their franchises with new games every 1–2 years.

Every genre has a shot to be a hit, but based on the historical data, sports, action, and shooter genres take the biggest portion of the cake.

There’s a clear positive correlation between critic scores and sales. Games that average 8+/10 scores perform very well, while mediocre and bad games fall short.

Through the years, the video game industry has changed a lot. Before the 2000s, Japan was pivotal in the industry thanks to Nintendo, Sega, Square Enix, etc. There were many games that were released only in Japan in those days. As video games shift to mainstream media in the West, most sales start to come from North America. Microsoft launched the Xbox to compete against giant Japanese video game console brands like PlayStation and Nintendo. As the years come close to the 2020s, globalization and digitalization have reached their peak. Before these, it was not easy to access the physical copies of video games. Right now, digital video game stores like Steam and Epic Games make the process of finding games and buying much easier.

## Conclusion and Personal Note

Even though the dataset only covers before the 2020s, the video game industry is still going strong. It’s one of the most popular and immersive forms of entertainment. Virtual Reality is a potentially groundbreaking technology that’s waiting to reach its maximum potential to shape the industry. The future looks bright for video games and gamers.

Thanks for reading. This was my first standalone EDA project. I aim to continue developing more technical and soft skills, deepen business acumen, and dive into more complex datasets to try to narrate the hidden story within data.

[Here's my Medium profile link](https://medium.com/@onur.ozgur24)
