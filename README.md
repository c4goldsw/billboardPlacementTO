# Optimal Digital Map Placement in Toronto
A solution for determining the most optimal placement of location-based information maps throughout Toronto.

##The Team & Mentor
**Chris Goldsworthy**: Chris is a third-year undergraduate Computer Sciecne student at the University of Toronto.  He has an interest in machine learning and will be focusing on ML for the remainder of his undergraduate education.

**Danny Luo**: Danny is a third-year undergraduate Math and Physics student at the University of Toronto.  He has a growing interest in data science and is actively teaching himself about the field.

**Ryan Garnett**: Ryan is a geographist who works for the City, specializing in curating and using geospatial datasets.

##The Challenge
The City of Toronto wants to place digital billboard maps throughout the city in order to indicate landmarks or attractions within a certain area surrounding the map (i.e. a five minute walking radius).  Therefore, it is important to determine what the most optimal placement of the billboard maps is, as well as determining which maps are more valuable compared to other maps (i.e. ranking them based off their importance).

##Our Solution
We were first given the cultural landmark dataset, which consists of properties labelled by the city as being availabe for use for any kind of cultural event.  Therefore, our initial idea was that we should seek to cluster cultural landmarks together.  The centroid of each cluster could then be an approximate location for placing a billboard map.  K - Means lends itself to the task very well.  One question however is how many centroids should be chosen.  We decided to frame this in terms of a cost problem: we should aim to maximize K to find the best placement of signs, whilst setting a threshold for maximum area overlap for all signs to prevent redundancy.  The threshold can be chosen arbitrarily and is something that a city will need to consider when trying to deploy the billboard maps.

Next, we wanted to determine which areas signs are the most valuable, .

##Source Code


##Data Sets
