---
title:  "Classifying Swing Types and Predicting Which Swings Get Used"
layout: post
---
**Introduction**  

Over the past couple years, Baseball Savant has been releasing more and more data on the hitting side. Previously, the public only had access to launch angle and exit velocity at a pitch-by-pitch level, but now we have access to more granular metrics such as bat speed and swing length. These new metrics help us better understand how a hitter’s swing works, and not just what the result of the swing was.  

With this new data, it’s challenging to understand how each variable relates to each other, and if there are any natural groupings. Along with that, it would be interesting to know how a hitter’s swing changes given various situational factors and the pitches that he is seeing. This can be modeled out, but without any understanding of how these variables relate to each other, it’s difficult to interpret how a hitter’s swing is changing.  

In this article, I aim to tackle both questions, by presenting a method to classify swings into groups based on their swing characteristics, and then predicting what type of swing a hitter will use given the pitch he sees and the situation that he is in.  

**Classification Explanation**  

The first step is creating global swing classifications. Instead of classifying swings separately for each batter, the classification is applied across the entire sample of swings.  

For each swing, there are five variables: attack angle, attack direction, swing path tilt, bat speed, and swing length. Attack direction is a function of the side of the plate the hitter is (pulling the ball is a different direction for a lefty than a righty), so I normalize to make all hitters right-handed. From here, I use a Gaussian Mixture Model (GMM) to classify swings based on these five variables.  

Choosing the number of swing types is somewhat of an art. Although GMMs use BIC to penalize overly complex models, the large sample size of 300k observations still leads the algorithm to add more clusters. These extra clusters yield only small improvements in fit, and so I manually set the cutoff to be 10 unique swing types. With 10, I felt that there was enough difference in the main clusters to be meaningful, while also giving check swings and outlier swing types their own group.  

The data that I am using is from the 2024 season. I wanted to use a full season of data, and both 2023 and 2025. This captures ~300k swings, and I don’t think swings have changed drastically from 2023 to 2024 or 2024 to 2025 that would make using a single season change classifications that drastically.  

One last thing to note is that certain variables (attack angle, swing length, and swing path tilt) are moderately influenced by location. Since these swing classifications are being fed into a model that has location as a predictor, this was fine, but a “location-neutral” swing classification would be useful to better understand these swings in isolation.  

**Classification Summary**  
Let’s see how the swings break down.  

![](/assets/photos/swing_classifications.png)    

I’m not creative enough to come up with names for each swing, but we can see here that most swings can be broken down into four main types. In the top four, there are two swings that are faster with a longer swing length (Swing Type 3 and 1), and two swings that are slower but shorter (4 and 9). Swing path tilt remains fairly constant across all types, and the faster swings have higher attack angles than the slower ones do.  

At a hitter level, most hitters have three main swings. Some have two, some have six, but in general, hitters rotate between three and four swing types.  

**Prediction Swing Type By Hitter**  

While classifying swings is interesting enough, the question “What type of swing will a batter use in this scenario?" is a bit more interesting, in my opinion. To answer this, I turned to CatBoost and built a multiclassification model to predict what type of swing a hitter will use given the location of the pitch, what the count is, and who the batter is. There are other relevant variables, like the velocity of the pitch and how many runners on base there are, but I think this representation (where the pitch thrown and what’s the situation) gets at the heart of the question well and shows how batter swing types evolve over the course of an at-bat, while being easy to visualize.

I used CatBoost because there are a lot of categorical features (12 unique counts + 650 batters), which made it a natural fit. In-sample accuracy was ~47%, a significant improvement over a naïve model which would guess 31% of swings correctly (since that was the most common swing type).  

**Predicted Swing Type Comparisons**  

To see how hitters have different approaches, let’s compare Alex Bregman and Cody Bellinger.

![](/assets/photos/bregman_plot.png)     

We can see here that Bregman is extremely consistent. Inside of the zone, he only uses one swing (Swing Type 3), which has an average 72 MPH bat speed and 7 ft swing length. When he chases above the zone, he uses Swing Type 9, which is slower (66.8 MPH) but shorter (6.0 ft). When he chases down, he uses Swing Type 1, which is faster (70.9 MPH) and longer (8.0 ft). Overall, there is relatively little variation in the types of swings that he uses.  

![](/assets/photos/bellinger_plot.png)  

In contrast, Bellinger uses multiple different swings within the zone. He uses Type 3,4, and 8 in zero and one strike counts. When he gets to two strikes, he adds Type 5 for pitches down and away. The swing types follow a similar arc to Bregman (slower and shorter up in the zone, faster and longer down), but Bellinger has multiple distinct swings within the strike zone whereas Bregman uses just one.    

This is just one comparison that can be made. I put together a Shiny app for every hitter in the 2024 season, which has these plots along with a table for each player that shows the characteristics of the swing along with Whiff%, Barrel% and wOBAcon for each of their swings. The link is at the bottom.  

**Conclusion**  

Hopefully this was an interesting exploration into classifying swing types and seeing how hitters change their swings in various situations. It will be interesting to have another full season of data once the current season wraps up to see how hitters have changed their approach at the plate. In the future, this work can be expanded upon to be used for specific batter-pitcher matchups and to think about what pitch shapes work best against various swing types.  

**Additional links**  
Shiny app (best viewed on desktop): [Predicted Swing Type Shiny App](https://david-gerth.shinyapps.io/predicted-swing-type/)



