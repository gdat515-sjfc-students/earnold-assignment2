\`\`\`

Introduction to Data file
-------------------------

I acquired this data from the World Health Organization. I was
interested in exploring some epidemic data to see how a disease would
compare between regions around the world, and whether that has changed
in recent years.

I found a sufficent dataset here
<a href="http://apps.who.int/gho/data/node.main.617?lang=en" class="uri">http://apps.who.int/gho/data/node.main.617?lang=en</a>

It addresses the HIV/AIDs epidemic, and this particular data covers the
number of people (all ages) living with HIV around the world. It
includes data from 2000, 2005, 2010, and 2018, across 172 different
countries.

I actually edited the data and added a column for region, so I would be
able to see this data more broadly at a regional level as well.

After reading in the data, I needed to clean it up a bit to get it ready
for ggplot2.

``` r
#read in data
hiv.data <- read.csv("C:/Users/Emily/Documents/Data Science/Data Visualization/Assignment 2/HIV.csv")

#need to create a column for year

hiv.data1 <- hiv.data %>% 
  gather(.,key = year, value = people, -Country, -Region)
```

    ## Warning: attributes are not identical across measure variables;
    ## they will be dropped

``` r
#minor text edits
hiv.data1$year <- gsub("X", "", hiv.data1$year)
hiv.data1$people <- gsub(" ", "", hiv.data1$people)
hiv.data1$people <- gsub("No data", NA, hiv.data1$people)


#making people numeric
hiv.data1$people <- as.numeric(hiv.data1$people)
```

    ## Warning: NAs introduced by coercion

``` r
hiv.data1$year <- as.factor(hiv.data1$year)

hiv.data1 <- na.omit(hiv.data1)
```

Now that I have this created and cleaned, the question I want to start
with is over time, which regions of the world represent the highest HIV
positive population?

``` r
hiv.data1 %>%
group_by(Region, year) %>% 
summarize(people = sum(people)) %>% 
ggplot(., mapping = aes(x = year, y = people, color = Region))+
   geom_point()+
  scale_y_log10()+
   xlab("Year")+
   ylab("Number of people living with HIV")+
  ggtitle("Number of people living with HIV by year")
```

![](earnold-assignment2_files/figure-markdown_github/create%20visualization-1.png)

Based on this first visualization, we can see that Africa has the
highest number of cases, which is steadily rising. The Americas come in
second, followed by South-East Asia.

I’m curious about the Americas’ cases in particular, since this Region
spans both North and South America.

I’ll group by the Americas and facet by year to see which countries are
contributing the most.

``` r
hiv.data1 %>%
filter(Region == "Americas") %>% 
ggplot(., mapping = aes(x = Country, y = people, color = Country))+
  geom_col(aes(fill = Country))+
  facet_wrap(~year)+
  xlab("Country")+
  ylab("Number of people living with HIV")+
  ggtitle("Number of people living with HIV by Country in the Americas", subtitle = "Faceted by Year")+
  theme(axis.text.x=element_blank(),
        axis.ticks.x=element_blank())
```

![](earnold-assignment2_files/figure-markdown_github/create%20visualization%202-1.png)

I think the most important takeaways from this visual is that Brazil
consistently represents the highest count of HIV cases, except for in
2010 when the USA experienced a notable spike. Digging further into the
data, it looks like data from the US is missing for all years shown here
except 2010.

I think this visualization is able to quickly answer the question of
which Region contains the most cases of HIV (Africa), and even digs
further to see which part of the Americas has the most HIV as well
(Brazil, and probably the US).

I think if I were to dig further into this, I would look up the
populations of all these countries to get an idea of how bad the
epidemic is within the context of a country’s population overall.
