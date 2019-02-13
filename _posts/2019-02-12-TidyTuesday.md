---
layout: post
title: First time #TidyTuesday
---

I'll keep this one short.  

I've finally decided to take the plunge on #TidyTuesday.  For those new to the concept tidytuesday is a project by the R4DS (R for Data Science) learning community designed for learners and practitioners to hone their R skills, specifically the packages in the tidyverse (dplyr, tidyr, and ggplot to name a few).  If you would like to get a little more acquinted please visit their site [here](https://github.com/rfordatascience/tidytuesday).

The 2019-02-05 data set is centered around the House Pricing Index.  Although 3 data sets were provided I focused on the `state_hpi` data set.  I wanted to find out the trend between my current home state of New Jersey against the National (US) average.

I created an annual average and then calculated the difference.  It seems the past couple of years have seen HPI in New Jersey fall compared to the National average.  Might be a good time to buy before the trend corrects itself!

![TidyTuesday](https://github.com/jmmnyc/tidytuesday/blob/master/Tidytuesday_2019_02_05.png)


My code is below:

```R
library(tidyverse)

state_hpi <- readr::read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-02-05/state_hpi.csv")
mortgage_rates <- readr::read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-02-05/mortgage.csv")
recession_dates <- readr::read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-02-05/recessions.csv")


difference_vs_national <- state_hpi %>%  group_by(year,state) %>% 
  summarise(state_annual_avg = mean(price_index), national_annual_avg = mean(us_avg)) %>% 
  mutate(difference_vs_national = state_annual_avg - national_annual_avg) %>% 
  select(year, state, difference_vs_national) 

difference_vs_national$vs_National <- ifelse(difference_vs_national$difference_vs_national>0,"above","below")

difference_vs_national %>% 
  filter(state == "NJ") %>% 
  ggplot(aes(x = year, y = difference_vs_national, fill = vs_National)) + 
  geom_col() + scale_fill_brewer(palette = "Set2") + 
  theme_bw() + geom_hline(yintercept = 0, color = "#333333") + 
  labs(title = "Annual House Price Index trend vs. National Average", 
       x = "Year", 
       y = "Difference vs. National avg",
       caption = "\n Source: Freddie Mac House Price Index\n by: Jose M @sanzbx") + 
  theme(legend.position = "top")
```
