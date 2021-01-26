# My Data Analysis Projects

Using R, SQL, and other sources to find identify and solve to problems with data.

NBA Team Efficiency Analysis
Tariq Abubakr
1/25/2021
When it comes to winning in the National Basketball Association (NBA), is it smarter to sign a superstar (or two) to a max contract or divide your team’s salary cap among a handful of good, but not elite, players? How can we determine which NBA teams are the most efficient at winning given their salary cap distribution? And what are the historical trends of team efficiency, and what do these trends indicate about future trends?

These are the questions I attempt to answer using available data. First, I had to determine a way to calculate a team’s efficiency in terms of their payroll. (An efficient team is one in which it wins a lot of games while paying the least amount of money possible to their highest-paid player, and vice versa for an inefficient team). The method I settled on was dividing the amount of games a team won in a given regular season by the percentage of the salary cap that a team’s highest-paid player received. For example, if team X won 50 games in a season, and their highest-paid player received 40 percent (.4) of X’s salary cap, then X’s team efficiency score (TES) would be 125 — the higher the score, the better. While not perfect, I felt this method would do a good enough job of estimating the effect that a superstar had on a team versus a team with a more normal distribution of salary — and hence, talent.

I used Basketball Reference, ESPN, and Wikipedia to pull data, as well as Excel and SQL to assist with calculations and data cleaning. Using R, I plotted each team’s TES. Here’s what I found:

library(ggplot2)
library(knitr)
library(tidyverse)
library(readxl)
library(sqldf)
nba_2018_efficiency <- read_excel("~/Documents/nba_2018_efficiency.xlsx")
ggplot(nba_2018_efficiency, aes(Efficiency,W)) + geom_point(size=3,color='purple') + labs(title="2018 Team Efficiency")


As you can tell, there is a clear correlation between a team’s TES and the number of games they win. The correlation coefficient is 0.66, to be exact, indicating that the ‘efficiency’ and ‘wins’ variables are strongly correlated. Of the sixteen teams with the top efficiency scores in the 2017–18 season, only two missed the playoffs (Sacramento and Chicago). However, it appears that once a team reaches a TES of about 175, there are diminishing returns to the additional number of games they win. This would indicate that having a more normal distribution of talent on a team is a good strategy for winning enough games to make it into the playoffs, but teams that want to win an even larger percentage of games would be smart to invest in a superstar or two, even at the expense of more depth.

top_6_2018 <- sqldf("SELECT season17_18,Percentage_of_SalaryCap,W,Efficiency FROM nba_2018_efficiency ORDER BY W DESC LIMIT 6")
top_6_2018
##   season17_18 Percentage_of_SalaryCap  W Efficiency
## 1         HOU               0.2855842 65   227.6036
## 2         TOR               0.2896643 59   203.6841
## 3         GSW               0.3500000 58   165.7143
## 4         BOS               0.3000000 55   183.3333
## 5         PHI               0.2321052 52   224.0363
## 6         CLE               0.3359037 50   148.8522
This theory is visible in the table above. The teams with the 6 best records all had superstar(s) on their roster: James Harden, DeMar DeRozan, the Big 4 in Golden State, Kyrie Irving, Joel Embiid, and LeBron James, respectively.

rest_of_teams <- sqldf("SELECT season17_18,Percentage_of_SalaryCap,W,Efficiency FROM nba_2018_efficiency WHERE W<50 ORDER BY W DESC LIMIT 10")
rest_of_teams
##    season17_18 Percentage_of_SalaryCap  W Efficiency
## 1          POR               0.2639244 49   185.6592
## 2          IND               0.2119221 48   226.4983
## 3          UTA               0.2217585 48   216.4517
## 4          NOP               0.2592178 48   185.1725
## 5          OKC               0.2879175 48   166.7144
## 6          MIN               0.1947773 47   241.3012
## 7          SAS               0.2165744 47   217.0155
## 8          DEN               0.3155544 46   145.7752
## 9          MIL               0.2267760 44   194.0241
## 10         MIA               0.2552086 44   172.4080
If we look above at the next 10 teams with the best records, we see that almost none of them have a verifiable superstar on their roster; many of them have high efficiency scores, however (above 200). This further supports the aforementioned theory.

How do these findings compare to past seasons and eras? Is there a noticeable trend either way regarding the differences? I decided to look at the 2007–08 and 1999–2000 seasons in order to get a full picture of seasons spanning three separate eras. This is what I found:

nbasalaries_2008 <- read_excel("~/Documents/nbasalaries_2008.xlsx")
nba2000_efficiency <- read_excel("~/Documents/nba2000_efficiency.xlsx")

ggplot(nbasalaries_2008, aes(Efficiency,W)) + geom_point(size=3,color='orange') + labs(title="2008 Team Efficiency")


ggplot(nba2000_efficiency, aes(Efficiency,W)) + geom_point(size=3,color='navy') + labs(title="2000 Team Efficiency")


It turns out there is a noticeable trend here. The further back we go in time, the less the median TES. In 2018, the median TES was 172; in 2008, it was 157; and in 2000, it was 145. However, the median number of wins stays roughly the same (between 41 and 44) in all three seasons.

This indicates that efficiency was less important to winning back then than it is now. To confirm this, I calculated the two graphs’ correlation coefficients: 0.20 for the 1999–00 season and 0.72 for the 2007–08 season. In other words, salary cap efficiency and wins were not correlated at all in 2000, while it was strongly correlated in 2008. This makes sense given the linear nature of the 2008 scatterplot.

top_6_2000 <- sqldf("SELECT Name,Team,Percentage_ofSalaryCap,W FROM nba2000_efficiency ORDER BY Percentage_ofSalaryCap DESC LIMIT 6")
top_6_2000
##                  NAME                   TEAM Percentage_ofSalaryCap  W
## 1 Shaquille O'Neal, C     Los Angeles Lakers              0.5041765 67
## 2   Kevin Garnett, PF Minnesota Timberwolves              0.4942941 50
## 3  Alonzo Mourning, C             Miami Heat              0.4412941 52
## 4    Juwan Howard, PF     Washington Wizards              0.4411765 29
## 5  Scottie Pippen, SF Portland Trail Blazers              0.4351471 59
## 6     Karl Malone, PF              Utah Jazz              0.4117647 55
The top players back then made up a much larger portion of a team’s salary than they do now. In 2000, Shaquille O’Neal alone made up half of the Lakers’ salary cap. The strategy paid off, as the Lakers ended up winning the 2000 NBA title. That strategy is also empirically less apparent these days as depth is more crucial to team success, possibly due to the faster pace of today’s game. In 2018, Kevin Durant was the player who took up the most of any team’s salary cap — 35%, a thirty percent decrease from Shaq’s salary cap hit. Like Shaq’s Lakers, Durant’s Warriors ended up winning the 2018 title.

Conclusion
These trends make it clear that it is much more important nowadays than it was back then to have a balanced roster salary-wise in order to win enough games to make the playoffs. Having four or five solid players on your roster is a smart way to ensure you’ll make your way into the playoffs. However, if you want to put your team over the top, you’ll likely need to sign a superstar to a max contract. This reinforces what we may have already known; the NBA is a league where you almost always need superstars in order to win a very high percentage of games — and therefore championships.

