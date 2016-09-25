# Optimal Information Map Placement in Toronto
A solution for determining the most optimal placement of location-based information maps throughout Toronto.

##The Team & Mentor
Chris Goldsworthy and Danny Luo are third-year students at the University of Toronto who have known each since first year. They both happened to be participating in HackOn(Data) - after finding out that they were both involved in the hackathon, they decided to work together.  Because of their background in computer science and math, they had a good grasp of the basic tools needed to copmlete data science related tasks. Their mentor, Ryan Garnett, is a geographist who works for the City, specializing in curating and using geospatial datasets.

With the guidance of their mentor Ryan and their preparation over the week preceding the final event, Danny and Chris sucessfully managed to create a model that attempted to find the most optimal placement for the information signs.  They had a great time listening to the various speakers at HackOn(Data) and were able to meet similar-minded data enthuasiasts. 

##The Challenge
The City of Toronto wants to place digital billboard maps throughout the city in order to indicate landmarks or attractions within a certain area surrounding the map (i.e. a five minute walking radius).  Therefore, it was important to determine what the most optimal placement of the billboard maps is, as well as determining which maps are more valuable compared to other maps (i.e. ranking them based off their importance).

We decided to work on this project because it was open-ended and required the use of unsupervised learning and creative approaches. We were able to use additional datasets to augment the original dataset provided by the City. 

##Our Solution
We were first given the cultural landmark dataset, hosted on TranQuant, which consists of properties labelled by the city as being available for use for any kind of cultural event (below: cultural landmarks in orange, transit stops in green for reference).  

![Culture and transit](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/culturalLandmarks.png)

Due to the small size of the dataset, we decided to use ipython notebook to do the main analysis and databricks notebook to mine social media data. 

Our initial idea was that we should seek to cluster cultural landmarks together.  The centroid of each cluster could then be an approximate location for placing a billboard map.  K - Means lends itself to this task very well.  However, one important question is how many centroids should be chosen.  We had wanted to frame this in terms of an optimization problem, but we struggled to devise a clear-cut loss metric.  Therefore, we decided to create a loss metric based off the overlap of the area surroudning each sign - we defined this area to be anywhere within a five minute's walking distance of each sign (these areas are represented by circles surroudning each sign placement in the graphs below).  We then chose to place approximately 200 signs, based on the overlap penalty and visual inspection.

We visualized the results of the K - Means clustering in the next figure, in which we divided the city according to what cluster it belonged to.

![K - Means Clustering](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/a2a498dc4ffd27e0c370efeb43a4578f06483e0b/images/kmeansRegion.png)

Next, we wanted to determine which areas signs are the most valuable and rank them accordingly.  There are several ways to determine which signs are the most valuable, but intuitively, it can be framed as areas with:

1. The highest volume of people participating in cultural activities (cultural popularity)
2. The most cultural landmarks
3. The highest degree of accessibility (which we framed in terms of accessibility by public transport)

After framing the problem this way, each sign can receive a score for each criteria above:

1. We can compute a score for cultural popularity by computing the number of Foursquare checkins (to cultural venues) within the area surrounding the sign (i.e. five minutes walk radius)
2. To see which area has the most cultural landmarks, we simply sum the number of landmarks in the area surrounding a sign
3. To determine which areas are the most accessible by public transport, we sum the number of transit stops in the area the sign covers

To compute these scores, we first mined checkin data off of Foursquare using a grid search, resulting in the graph below:
![Checkins](https://github.com/c4goldsw/billboardPlacementTO/blob/a2a498dc4ffd27e0c370efeb43a4578f06483e0b/images/checkins.png)

We then ranked the signs based on their popularity on social media.
![checkin score](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/checkinScore.png)

Using the City of Toronto transit stop dataset, we ranked the signs based on their proximity to transit stops in the graph below.
![Transit](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/a2a498dc4ffd27e0c370efeb43a4578f06483e0b/images/transitScore.png)

We normalized the scores based on their z-score to put them all on the same scale. Finally, we computed the overall score using a weighted sum of the three scores ("Final Score").

![weighted sum score](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/finalScore.png)

The top five of these signs using the weighted score can be seen below:

![top 5 weighted](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/finalScoreTop5.png)

##Possible Improvements
###Optimization Problem
Our first major improvement would be to make a formal optimization problem. This would allow us to make a rigourous machine learning problem and reduce human bias in our solution. This was difficult to implement given our time constraints and the open-endedness of the challenge. Reducing human bias will also involve creating simpler, more transparent scoring techniques, which is currently a major source of human bias, due to our chosen importance weighting for our various scoring factors (e.g. public transport proximity vs. social media popularity). Our weights were chosen rather arbitrarily, but given a well-stated optimization problem with ample labelled training data, we may optimize them with machine learning methods. 

###Sign Placement
Finally, instead of placing signs in the middle of a cluster (which could be anywhere), we can simply place signs in the nearest intersection with the most pedestrian traffic. 

##Implementation
After producing a reasonable model based on k-means and popularity scoring, our partners from the City of Toronto can use our results to help them determine optimal sign placements. Naturally, there are many factors that are involved with such a project that are not represented in the data (i.e. equal distribution of signs among city wards).   

More generally, our analysis can be used to determine hotspots over Toronto for various business purposes. The social media data can be filtered to focus on specific demographics and where in Toronto they are most active. In conclusion, our model is broad and contains many different possibilities for further specialized uses, for both governmental and business purposes. 

##Source Code

The code used to create some of these images, as well as mining data off of Foursquare, [can be found here](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/3369275223907376/2553738767828155/6070394398366266/latest.html).

The code used to compute the location and ranking of the signs can be found 
in the repository.

##Data Sets
* [Cultural Landmarks](http://tranquant.com/datasource-detail/b9bcabac-a037-4bdc-9b81-f4d1fe03f47b)
* [Transit Stops](http://www1.toronto.ca/wps/portal/contentonly?vgnextoid=96f236899e02b210VgnVCM1000003dd60f89RCRD)
* [Mined Foursquare data](https://github.com/c4goldsw/billboardPlacementTO/blob/master/code/checkins_final.txt)
