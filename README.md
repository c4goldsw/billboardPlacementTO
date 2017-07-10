# Optimal Digital Map Placement in Toronto
A solution for determining the most optimal placement of location-based information maps throughout Toronto.

## The Team & Mentor
**Chris Goldsworthy**: Chris is a third-year undergraduate Computer Science student at the University of Toronto.  He has an interest in machine learning and will be focusing on ML for the remainder of his undergraduate education.

**Danny Luo**: Danny is a third-year undergraduate Math and Physics student at the University of Toronto.  He has a growing interest in data science and is actively teaching himself about the field.

**Ryan Garnett**: Ryan is a geographist who works for the City, specializing in curating and using geospatial datasets.

## The Challenge
The City of Toronto wants to place digital billboard maps throughout the city in order to indicate landmarks or attractions within a certain area surrounding the map (i.e. a five minute walking radius).  Therefore, it is important to determine what the most optimal placement of the billboard maps is, as well as determining which maps are more valuable compared to other maps (i.e. ranking them based off their importance).

## Our Solution
We were first given the cultural landmark dataset, which consists of properties labelled by the city as being availabe for use for any kind of cultural event (below: cultural landmarks in orange, transit stops in green for reference).  

![Culture and transit](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/culturalLandmarks.png)

Therefore, our initial idea was that we should seek to cluster cultural landmarks together.  The centroid of each cluster could then be an approximate location for placing a billboard map.  K - Means lends itself to the task very well.  One question however is how many centroids should be chosen.  We decided to frame this in terms of a cost problem: we should aim to maximize K to find the best placement of signs, whilst setting a threshold for maximum area overlap for all signs to prevent redundancy.  The threshold can be chosen arbitrarily and is something that a city will need to consider when trying to deploy the billboard maps.

![Overlap vs K](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/overlapPenalty.png)

Next, we wanted to determine which areas signs are the most valuable and rank them accordingly.  There are several ways to determine which signs are the most valuable, but intuitively, it can be framed as areas with:

1. The highest volume of people participating in cultural activities (cultural popularity)
2. The most cultural landmarks
3. The highest degree of accessibility (which we framed in terms of accessibility by public transport)

After framing the problem this way, each sign can receive a score for each criteria above:

1. We can compute a score for cultural popularity by computing the number of Foursquare checkins (to cultural venues) within the area surrounding the sign (i.e. five minutes walk radius)
2. To see which area has the most cultural landmarks, we simply sum the number of landmarks in the area surrounding a sign
3. To determine which areas are most accesiable by public transport, we sum the number of transit stops in the area the sign covers

To compute these scores, we first mined checkin data off of Foursquare using a grid search, resulting in the data below:
![Checkins](https://github.com/c4goldsw/billboardPlacementTO/blob/a2a498dc4ffd27e0c370efeb43a4578f06483e0b/images/checkins.png)

Finally, we can compute the scores of each area using the individual scores, or using a weighted sum of them ("Final Score").

![checkin score](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/checkinScore.png)

![weighted sum score](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/finalScore.png)

The top five of these signs using the weighted score can be seen below:

![top 5 weighted](https://raw.githubusercontent.com/c4goldsw/billboardPlacementTO/master/images/finalScoreTop5.png)

## Source Code

The code used to create some of these images, as well as mining data off of Foursquare, [can be found here](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/3369275223907376/2553738767828155/6070394398366266/latest.html).

The code used to compute the location and ranking of the signs can be found 
in the repository.

## Data Sets
* [Cultural Landmarks](http://tranquant.com/datasource-detail/b9bcabac-a037-4bdc-9b81-f4d1fe03f47b)
* [Transit Stops](http://www1.toronto.ca/wps/portal/contentonly?vgnextoid=96f236899e02b210VgnVCM1000003dd60f89RCRD)
* [Mined Foursquare data](https://github.com/c4goldsw/billboardPlacementTO/blob/master/checkins_final.txt)
