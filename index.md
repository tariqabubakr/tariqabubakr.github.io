---
title: "NBA Team Efficiency Analysis"
author: "Tariq Abubakr"
output:
 html_document: default
 pdf_document: default
---
---


---

<style type="text/css">
  body{
  font-size: 12pt;
}
</style>

When it comes to winning in the National Basketball Association (NBA), is it smarter to sign a superstar (or two) to a max contract or divide your team’s salary cap among a handful of good, but not elite, players? How can we determine which NBA teams are the most efficient at winning given their salary cap distribution? And what are the historical trends of team efficiency, and what do these trends indicate about future trends?

These are the questions I attempt to answer using available data. First, I had to determine a way to calculate a team’s efficiency in terms of their payroll. (An efficient team is one in which it wins a lot of games while paying the least amount of money possible to their highest-paid player, and vice versa for an inefficient team). __The method I settled on was dividing the amount of games a team won in a given regular season by the percentage of the salary cap that a team’s highest-paid player received__. For example, if team X won 50 games in a season, and their highest-paid player received 40 percent (.4) of X’s salary cap, then X’s team efficiency score (TES) would be 125 — the higher the score, the better. While not perfect, I felt this method would do a good enough job of estimating the effect that a superstar had on a team versus a team with a more normal distribution of salary — and hence, talent.


I used Basketball Reference, ESPN, and Wikipedia to pull data, as well as Excel and SQL to assist with calculations and data cleaning. Using R, I plotted each team’s TES. Here’s what I found:


```{r setup, message=FALSE, warning=FALSE}
library(ggplot2)
library(knitr)
library(tidyverse)
library(readxl)
library(sqldf)
```


```{r, warning=FALSE,out.width="65%",fig.align='center'}
nba_2018_efficiency <- read_excel("~/Documents/nba_2018_efficiency.xlsx")
ggplot(nba_2018_efficiency, aes(Efficiency,W)) + geom_point(size=3,color='purple') + labs(title="2018 Team Efficiency")
```

As you can tell, there is a clear correlation between a team’s TES and the number of games they win. The correlation coefficient is 0.66, to be exact, indicating that the ‘efficiency’ and ‘wins’ variables are strongly correlated. Of the sixteen teams with the top efficiency scores in the 2017–18 season, only two missed the playoffs (Sacramento and Chicago). However, it appears that once a team reaches a TES of about 175, there are diminishing returns to the additional number of games they win. This would indicate that having a more normal distribution of talent on a team is a good strategy for winning enough games to make it into the playoffs, but teams that want to win an even larger percentage of games would be smart to invest in a superstar or two, even at the expense of more depth.



```{r}
top_6_2018 <- sqldf("SELECT season17_18,Percentage_of_SalaryCap,W,Efficiency FROM nba_2018_efficiency ORDER BY W DESC LIMIT 6")
top_6_2018
```



This theory is visible in the table above. The teams with the 6 best records all had superstar(s) on their roster: James Harden, DeMar DeRozan, the Big 4 in Golden State, Kyrie Irving, Joel Embiid, and LeBron James, respectively.



```{r}
rest_of_teams <- sqldf("SELECT season17_18,Percentage_of_SalaryCap,W,Efficiency FROM nba_2018_efficiency WHERE W<50 ORDER BY W DESC LIMIT 10")
rest_of_teams
```



If we look above at the next 10 teams with the best records, we see that almost none of them have a verifiable superstar on their roster; many of them have high efficiency scores, however (above 200). This further supports the aforementioned theory.

***
How do these findings compare to past seasons and eras? Is there a noticeable trend either way regarding the differences? I decided to look at the 2007–08 and 1999–2000 seasons in order to get a full picture of seasons spanning three separate eras. This is what I found:



```{r,message=FALSE,warning=FALSE,out.width="65%",fig.align='center'}
nbasalaries_2008 <- read_excel("~/Documents/nbasalaries_2008.xlsx")
nba2000_efficiency <- read_excel("~/Documents/nba2000_efficiency.xlsx")

ggplot(nbasalaries_2008, aes(Efficiency,W)) + geom_point(size=3,color='orange') + labs(title="2008 Team Efficiency")

ggplot(nba2000_efficiency, aes(Efficiency,W)) + geom_point(size=3,color='navy') + labs(title="2000 Team Efficiency")
```



It turns out there is a noticeable trend here. The further back we go in time, the less the median TES. In 2018, the median TES was 172; in 2008, it was 157; and in 2000, it was 145. However, the median number of wins stays roughly the same (between 41 and 44) in all three seasons.


This indicates that efficiency was less important to winning back then than it is now. To confirm this, I calculated the two graphs’ correlation coefficients: 0.20 for the 1999–00 season and 0.72 for the 2007–08 season. In other words, salary cap efficiency and wins were not correlated at all in 2000, while it was strongly correlated in 2008. This makes sense given the linear nature of the 2008 scatterplot.



```{r}
top_6_2000 <- sqldf("SELECT Name,Team,Percentage_ofSalaryCap,W FROM nba2000_efficiency ORDER BY Percentage_ofSalaryCap DESC LIMIT 6")
top_6_2000
```



The top players back then made up a much larger portion of a team’s salary than they do now. In 2000, Shaquille O’Neal alone made up half of the Lakers’ salary cap. The strategy paid off, as the Lakers ended up winning the 2000 NBA title. That strategy is also empirically less apparent these days as depth is more crucial to team success, possibly due to the faster pace of today’s game. In 2018, Kevin Durant was the player who took up the most of any team’s salary cap — 35%, a thirty percent decrease from Shaq’s salary cap hit. Like Shaq’s Lakers, Durant’s Warriors ended up winning the 2018 title.

*** 

## Conclusion
These trends make it clear that it is much more important nowadays than it was back then to have a balanced roster salary-wise in order to win enough games to make the playoffs. Having four or five solid players on your roster is a smart way to ensure you’ll make your way into the playoffs. However, if you want to put your team over the top, you’ll likely need to sign a superstar to a max contract. This reinforces what we may have already known; the NBA is a league where you almost always need superstars in order to win a very high percentage of games — and therefore championships.

*** 
