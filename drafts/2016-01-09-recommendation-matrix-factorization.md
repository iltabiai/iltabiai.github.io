---
layout: post
title:  "Recommendation algorithm based on matrix factorization"
date:   2016-01-09 22:30:00
comments: true
categories: [machine learning]
tags: python machinelearning
---

Podcasts are a growing business today, which is good news for me since I am a huge fan of this new media. The trick is that podcasts are everywhere, too many of them, hidden throughout the web. It is pretty hard to find new podcasts, and it is even harder to test all of them until I find a few I actually like.
In order to solve this problem, I decided to take a look at the solutions developed and used by Amazon or Netflix. The simplest algorithm to do this job is the [Non-Negative MAtrix Factorization](https://en.wikipedia.org/wiki/Non-negative_matrix_factorization). I had studied matrix factorization, especially the [LUA decomposition](https://en.wikipedia.org/wiki/LU_decomposition), but for completely different purposes (finding the best solution to accomplish a mechanical function while maximizing certain outputs). This method is also interesting because it allows to discover existing links between *users* and *items*.

Ok, so to make this recommendation algorithm we need:

    * List of users
    * List of podcasts (or anything else)
    * Ratings (between 0 and 10 for example) made by each user
    
Each user is going to rate some of the podcasts (each user *does not have to rate all podcasts*).

<table style="width:100%">
  <tr>
    <td></td>
    <td>Podcast 1</td> 
    <td>Podcast 2</td> 
    <td>Podcast 3</td> 
    <td>Podcast 4</td> 
    <td>\\(\ddots\\)</td> 
    <td>Podcast 5</td> 
  </tr>
  
  <tr>
    <td>
    User 1
    </td>
  </tr>
  <tr>
    <td>
    User 2
    </td>
  </tr>
  <tr>
    <td>
    User 3
    </td>
  </tr>
</table>

|     	|Pod1  	|Pod2  	|Pod3  	|Pod4  	|...    |Pod5
|---	|---	|---	|---	|---	|---    |
|User 1	|   	|   	|   	|   	|...    |
|User 2	|   	|   	|   	|   	|       |
|User 3	|   	|   	|   	|   	|       |
|...    |       |       |       |       |       |
|User n |       |       |       |       |       |


\\(R = P \times Q^T \\)


http://scikit-learn.org/stable/modules/generated/sklearn.decomposition.NMF.html

http://fr.slideshare.net/studentalei/matrix-factorization-techniques-for-recommender-systems
http://www.quuxlabs.com/blog/2010/09/matrix-factorization-a-simple-tutorial-and-implementation-in-python/


{% include twitter_plug.html %}

