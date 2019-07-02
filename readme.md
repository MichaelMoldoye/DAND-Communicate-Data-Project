# Exploring Kickstarter Project Data
## by Michael Mosin


## Dataset

> Provide basic information about your dataset in this section. If you selected your own dataset, make sure you note the source of your data and summarize any data wrangling steps that you performed before you started your exploration.

The original data consisted of approximately 3786 projects and 37 variables. The data was downloaded from the CSV link under "2019-05-16" on the website https://webrobots.io/kickstarter-datasets/. 

I started off my wrangling by exploring the dataset via the .head() and .info() methods. The issues I found were as follows:

### Quality:
- Variables "friends", "is_backing", "is_starred", and "permissions" only have one entry and should be dropped
- Per https://help.kickstarter.com/hc/en-us/articles/115005135834-What-is-Spotlight-:
  - Variable 'spotlight' should be dropped, since it only applies to successful projects after the fact, and is thus not a predictor of anything.
- Variables "created_at", "deadline", "launched_at", and "state_changed_at" are set in unix time instead of readable datetime
- Variables with financial values such as "converted_pledged_amount", "goal", "pledged", and "usd_pledged" are set to different decimal places, and should be rounded to at most two decimal places
- Only two entries are missing data for "location" (not a big deal, given that we have "country" data; these)
- Only eleven entries are missing data for "usd_type" (this variable is not important to the investigation)
### Tidiness:
- Data entries in the columns "category", "creator", "location", "photo", "profile", and "urls" contain multiple pieces of information. If separated, they could be their own dataframes or made into additional columns in the main dataframe.
  - The "category" variable can garner category and sub-category info for the projects
  - The "location" variable can garner data regarding the project's state name, city name, and city type
  - The "creator","photo", "profile", "urls" variables have no data that is relevant to this project and should be dropped

Here are the steps I took to wrangle the dataset prior to exploration:
- Remove (essentially) empty columns and 'spotlight'
- Fix time categories to datetime
- Fix financial categories via rounding
- Address tidiness issue of "category" variable by creating variables holding extracted values for main category and sub-category
- Address tidiness issue of "location" variable by creating variables holding extracted values for state, city, and type of city
- Remove 16 unnecessary variables
- Engineer features related to the time variables: 'created_at', 'launched_at', 'deadline', 'state_changed_at'
- Engineer feature: proportion of project funding relative to goal

Throughout the exploration, I continued making adjustments to the dataset, such as:
- Isolate and use sub-dataset which excludes projects that are 'live'
- Remove variable 'as_starrable'
- Engineer boolean variable 'successful' out of 'state'
- Engineer variable 'avg_donation' out of 'usd_pledged' and 'backers_count'

## Summary of Findings

> Summarize all of your findings from your exploration here, whether you plan on bringing them into your explanatory presentation or not.

- More projects succeeded than not: 2196 out of 3669
- Of projects in a final state, 5.7% ended early - they were either cancelled or suspended; the remaining 94.3% either succeeded or failed by their deadline
- There is a VERY wide distribution of backers, skewed by many projects having zero or few backers, and a few outlier projects with tens of thousands of backers
- The log transformation of the number of backers illustrates a bimodal distribution - around 0 to 1 backers for failed projects, and around 40 to 60 backers for successful ones
- Nearly 1/11 projects had 0 backers. Over one quarter of projects had 5 or fewer backers
- Starting at 9 contributors, campaigns may have more than a 50% chance of succeeding
- Almost all projects originated out of a Town type of location. A few were submitted from County, Suburb, Island.
- The vast majority of project campaigns are based in the US, followed by other English-speaking countries. There is some visible representation from Northern Europe, Mexico, Western Europe, and Hong Kong
- Median Proportion of Goal Funded: 1.0254. Maximum: 27588.23.
- The projects whose Proportion of Goal Funded was past the 99th percentile had received more than 18.7 times their goal. In some cases the project's funding proportion was severely greater due to having a campaign goal as low as several hundred dollars - sometimes even ONLY $1.
- The distribution of Proportion of Goal Funded is exponential and bimodal - at 0 (or nearly 0, for those campaigns which received nothing or nearly nothing), and at 1 (representing campaigns that met their goal or even surpassed it by a little bit).
- There appears to be a good quantity of campaigns that didn't meet their goal, despite still getting some funding.
- Nothing too apparent stands out in the scatter matrix of the variables 'backers_count', 'goal', funded_prop', 'days_to_launch', 'days_to_succeed'. Most distributions are just reflective of the independent variable's distribution (i.e. backers_count vs days_to_launch resembles the distribution of days_to_launch).
- Some project categories are very common and successful for Kickstarter campaigns: Music, Technology, Publishing, and Theater. However, even some less common categories have relatively similar success rates, if not better: Comics, Fashion, Film & Video, Art, Photography. A few that are less successful are Food, Crafts, Dance, Games, Design, Journalism.
- Almost a fifth of successful campaigns were Staff Picks. However, being a Staff Pick does not guarantee success, as can be seen by the 4% of failed campaigns and 6% of canceled campaigns being Staff Picks.
- Via visual assessment, campaigns that were selected as Staff Pick were more likely to succeed.
- Project Categories of Crafts, Food, Dance, Games, and even Design are categories that have difficulty getting more than half of their projects funded. For Crafts, Food, and Dance, it appears that a low backer count is a contributing factor.
- Not all technology subcategories are equally successful. Those subcategories which appear to acquire much more funding than requested (and have more than 75% of projects meeting goals) include Gadgets, DIY Electronics, Technology (a bit vague, no?), Sound, and 3D Printing. Apps, Web, Makerspaces, and Space Exploration also have the majority (if not all) of their projects being successful, however not by the same margins. The subcategories which are more likely to not meet their goals are Wearables, Software, Fabrication Tools, and Flight.
- Campaigns that met or exceeded their goal (proportion funded >= 1) are successful. Those that were not able to do so are not.
- When comparing spread of projects by their Success relative to Average Donation and Number of Backers, it appeared that the fewer backers a project has, the less likely it will be successful, even with high average donations.
- From a logistic regression (success vs Staff Pick, number of backers, and average donations): For each one additional backer, success is 1.037 times as likely holding all else constant. For each additional dollar in an average donation, success is 1.0021 times as likely holding all else constant. Staff Pick is not a statistically significant predictor of project success

## Key Insights for Presentation

> Select one or two main threads from your exploration to polish up for your presentation. Note any changes in design from your exploration step here.

- The log transformation of the number of backers illustrates a bimodal distribution - around 0 to 1 backers for failed projects, and around 40 to 60 backers for successful ones. The distribution for failed projects is still exponential even after the transformation, whereas successful projects more resemble a normal distribution.
- When looking at the distribution for Proportion of Goal Funded, what is interesting is the exponential nature of both segments. I did conduct a log transform but it still left a curving exponential distribution due to the large range of successful proportions and the large density of campaigns with proportion values of 0 and 1. Since a successful campaign is defined as one which met its funding goal, then it comes as no surprise that the split occurs at the proportion of "1": all campaigns inclusive and higher are successful, all below - not so much.
- Projects which are selected as Staff Picks are more likely to succeed than those that aren't. In fact, almost a fifth of successful campaigns are designated as Staff Pick. Of course, there were a few Staff Picks that failed or were suspended.
- Projects in the Music, Technology, Publishing, and Theater categories are popular among both creators and backers.
However, even some less common categories have relatively similar success rates, if not better: Comics, Fashion, Film & Video, Art, Photography. A few that are less successful are Food, Crafts, Dance, Games, Design, Journalism.
- Looking at the median point for the boxplots relative to the "1" point on the y-axis, we can see the proportion of projects within each Main Category which met their goal or didn't. Since meeting one's funding goal (getting at least "1" for the funding proportion) defines a Kickstarter campaign success, we can see whether a majority of campaigns succeeded or failed.
- There is a slight impression that the fewer backers a project has, the less likely it will be successful, even with high average donations.

## List of resources used during the creation of the project:
- Udacity DAND classroom material, and example project material
- Pandas, Seaborn, Matplotlib, Numpy documentation
- https://stackoverflow.com/questions/49188960/how-to-show-all-of-columns-name-on-pandas-dataframe/49189503
- https://help.kickstarter.com/hc/en-us/articles/115005135834-What-is-Spotlight-
- https://stackoverflow.com/questions/19231871/convert-unix-time-to-readable-date-in-pandas-dataframe
- http://www.datasciencemadesimple.com/difference-two-timestamps-seconds-minutes-hours-pandas-python-2/ https://docs.scipy.org/doc/numpy/reference/arrays.datetime.html
- https://stackoverflow.com/questions/27041724/using-conditional-to-generate-new-column-in-pandas-dataframe)
- https://stackoverflow.com/questions/31749448/how-to-add-percentages-on-top-of-bars-in-seaborn
- https://stackoverflow.com/questions/33271098/python-get-a-frequency-count-based-on-two-columns-variables-in-pandas-datafra
- https://towardsdatascience.com/building-a-logistic-regression-in-python-step-by-step-becd4d56c9c8
