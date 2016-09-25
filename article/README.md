# Optimal Wayfinding-Information Map Placement in Toronto
A solution for determining the most optimal placement of location-based wayfinding-information maps throughout Toronto.

##The Team & Mentor
Chris Goldsworthy and Danny Luo are third-year students at the University of Toronto who have known each since first year. They both happened to be participating in HackOn(Data) - after finding out that they were both involved in the hackathon, they decided to work together.  Because of their background in computer science and math, they had a good grasp of the basic tools needed to complete data science related tasks. Their mentor, Ryan Garnett, is a geographist who works for the City, specializing in curating and using geospatial datasets.

With the guidance of their mentor Ryan and their preparation over the week preceding the final event, Danny and Chris created a model to find the most optimal placement for the wayfinding maps.  They enjoyed listening to the various speakers at HackOn(Data) and were able to meet similar-minded data enthuasiasts. 

##The Challenge
The City of Toronto wants to place wayfinding maps throughout the city in order to indicate landmarks or attractions within a certain area surrounding the map (i.e. a five minute walking radius).  Therefore, it is important to determine what the most optimal placement of the maps are, as well as determining which maps are more valuable compared to other maps (i.e. ranking them based off their importance).

We decided to work on this project because it was open-ended and required the use of unsupervised learning. It also required additional datasets to augment the original dataset provided by the City, in order to derive conclusions about the importance of individual maps. 

##Our Solution
We were first given the cultural landmark dataset which consists of properties labelled by the city as being available for use for any kind of cultural event. Due to the small size of the dataset, we decided to use a Jupyter notebook to do the main analysis (below: cultural landmarks in orange, transit stops in green for reference).  

![Culture and transit](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/culturalLandmarks.png)

Our initial idea was that we should seek to cluster cultural landmarks together.  The centroid of each cluster could then be an approximate location for placing a billboard map.  K - Means lends itself to this task very well.  However, one important question was how many centroids should be chosen (i.e. how many maps should be placed in the city).  We had wanted to frame this in terms of an optimization problem, but we struggled to devise a clear-cut loss metric during the final event.  Therefore, we decided to create a loss metric based off the overlap of the area surroudning each map - we defined this area to be anywhere within a five minute's walking distance of each map.  These areas are represented by circles surroudning each map placement in the graphs below.  We then chose to place approximately 200 maps, based on the overlap penalty and visual inspection (this is obviously a major issue and is addressed below in "Improvements for our Model").

We visualized the results of the K - Means clustering in the next figure, in which we divided the city according to which cluster it belonged to.

![K - Means Clustering](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/a2a498dc4ffd27e0c370efeb43a4578f06483e0b/images/kmeansRegion.png)

Next, we wanted to determine which maps are the most valuable and rank them accordingly.  There are several ways to determine which maps are the most valuable, but intuitively, it can be framed as areas with:

1. The highest volume of people participating in cultural activities (cultural popularity)
2. The most cultural landmarks
3. The highest degree of accessibility (which we framed in terms of accessibility to public transport)

After framing the problem this way, each map can receive a score for each criteria above:

1. We can compute a score for area popularity by computing the number of Foursquare checkins (to cultural venues) within the area surrounding the map (i.e. five minutes walk radius).  Note: Foursquare is a service that let's users check into and rate venues (e.g. parks, cinemas, museums, plazas, etc.).
2. To see which area has the most cultural landmarks, we simply sum the number of landmarks in the area surrounding a map
3. To determine which areas are the most accessible by public transport, we sum the number of transit stops in the area the map covers

To compute these scores, we first mined checkin data off of Foursquare using a grid search (this was done on Databricks using a notebook).  The collected checkin data can be see below:

![Checkins](https://github.com/c4goldsw/billboardPlacementTO/blob/a2a498dc4ffd27e0c370efeb43a4578f06483e0b/images/checkins.png)

We then ranked the maps based on the number of Foursquare checkins in the area surrounding the map.

![checkin score](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/checkinScore.png)

Using the City of Toronto transit stop dataset, we ranked the maps based on their proximity to transit stops in the graph below.

![Transit](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/a2a498dc4ffd27e0c370efeb43a4578f06483e0b/images/transitScore.png)

We normalized the scores based on their z-score to put them all on the same scale. Finally, we computed the overall score using a weighted sum of the three scores ("Final Score").

![weighted sum score](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/finalScore.png)

The top five of these maps using the weighted score can be seen below:

![top 5 weighted](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/finalScoreTop5.png)

##Improvements for our Model
Our first major improvement would be to make a formal optimization problem. This would allow us to make a problem that's can be suitably solved using machine learning. This was difficult to implement given our time constraints and the open-endedness of the challenge. Making our problem suitable for solving by machine learning methods will also involve removing as much human bias from our model as possible.  The weights in our final score were chosen rather arbitrarily and is a major source of human bias.  Creating a simpler, more transparent scoring technique may solve this.  An example would be to just look at one score (e.g. area popularity) and forming an optimization problem based off that (currently in progress).  Also, instead of placing maps in the middle of a cluster, which could be anywhere, we can simply place maps at the nearest major intersection. 

##Practical Usage of our Model
After producing a reasonable model based on k-means and popularity scoring, our partners from the City of Toronto can use our results to help them determine optimal map placements. Naturally, there are many factors that are involved with such a project that are not represented in the data, such as the equal distribution of maps amongst the city wards. Thus, our model must be used with discretion. 

More generally, our analysis can be used to determine hotspots over Toronto for various business purposes. If we were able to obtain combined demographic and location data from social media data, businesses could make more informed descisions to reach out to certain audiences more easily. In conclusion, our model is broad and contains many different possibilities for further specialized uses, for both governmental and business purposes. 

##Source Code
The code used to create some of these images, as well as mining data off of Foursquare, [can be found here](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/3369275223907376/2553738767828155/6070394398366266/latest.html).

The code used to compute the location and ranking of the maps can be found 
in the repository.

##Data Sets
* [Cultural Landmarks](http://tranquant.com/datasource-detail/b9bcabac-a037-4bdc-9b81-f4d1fe03f47b)
* [Transit Stops](http://www1.toronto.ca/wps/portal/contentonly?vgnextoid=96f236899e02b210VgnVCM1000003dd60f89RCRD)
* [Mined Foursquare data](https://github.com/c4goldsw/billboardPlacementTO/blob/master/code/checkins_final.txt)
