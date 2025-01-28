---
title:  "How Does Pitch Recognition Translate to Outfield Defense?"
layout: post
---
A pet theory I’ve had over the years was that hitters who have good pitch recognition skills are likely to be better defenders in the outfield than hitters who have poor pitch recognition skills. My thought was that if you are good at identifying pitches out of the hand while hitting, you’re likely to be good at seeing the ball off the bat in the outfield. After thinking it through a bit, I decided to sit down and attempt to test my theory to see if it holds up. Let’s take a look.  

Before looking at how well a pitch recognition metric may project defense, I first wanted to look at how well other granular measurements predict Outs Above Average. Baseball Savant releases a couple key measurements, average Reaction (feet covered in first 1.5 seconds after ball was hit), average Burst (1.6-3 seconds) and average Sprint Speed. When evaluating range for outfielders, typically we are thinking about a player’s reaction time, how quickly they accelerate, and their top end speed. Hareeb al-Saq wrote about this in his Fielding-Independent Defense piece, and I’ve built a similar model here, regressing outs per opportunity (Out%), where opportunities are plays with catch probability of 90% or lower, against Reaction, Burst, and Sprint Speed. From the posterior predictive check plot below, you can see that this is reasonable model.  

![](/assets/photos/post1.png)  

I’ve standardized predictors here, so that 1 standard deviation above average (or 60 grade in scouting parlance) results in having an X% higher Out%, holding all else constant. You can see that being 60-grade in Burst results in roughly an 8% higher Out%, whereas being 60-grade in Reaction is just over 1%. This is a surprising result, given how much we talk about the importance of the first step in defense, but this doesn’t necessarily show that to be the case.  

![](/assets/photos/post2.png)  

We’ve established that Reaction, Burst, and Sprint Speed provide a good model for predicting Out%, but let’s look at correlations with my hypothesis, which is that pitch recognition at the plate has an effect on fielding ability.  

To do so, we need a pitch recognition stat, and after testing a few different ones out, I found the best one to be “Hittable Pitches Taken” Rate (HPTR, number of takes in zone divided by number of takes overall), defined by Sky Kalkman in his Simple Seager article (SEAGER being a stat created by Robert Orr to evaluate swing decisions).  

![](/assets/photos/post3.png)  

We can see from the correlation plot here that the effect is fairly weak between Out% and pitch recognition. Note though that of the three variables we started with, Reaction is correlated the least with Out%, so if we were to pick up on an effect of pitch recognition translating to a better first step defensively, we’d still expect the effect of that on Out% to be modest relative to Burst and Sprint Speed. The correlation between Reaction and HPTR isn’t perfect but it is stronger than the correlation between pitch recognition and Out%, so I decided to go ahead and try prediction Out% using Sprint Speed and HPTR.  

Similar to the earlier model, I used Bayesian linear regression to predict Out% using sprint speed and HPTR, both of which were standardized. Since I have some prior intuition on how each variable should affect the model, I thought this would be a good time to use a Bayesian model and encode my prior intuition into that model. I used normal distributions for three parameters and then the exponential distribution for the standard deviation of the model.  

Because the average Out% in this sample is ~50%, I let the intercept have a prior distribution centered at 50, with a standard deviation of 5. I wouldn’t expect anyone who was average at both sprint speed and HPTR to have a Out% far off of the sample average, and the smaller standard deviation reflects that.  

For sprint speed, I typically think that if an outfielder is a plus (60-grade) runner, there’s a good chance that he ends up as a plus defender. A plus defender is roughly 12 Out% higher than average in this sample, and so I let the prior distribution be centered at 12, with a standard deviation of 6, given the model plenty of room to adjust from my prior intuition.  

For HPTR, while I didn’t think there was a strong effect, I figured it was slightly positive, and so I let the prior distribution be centered at 2, with standard deviation of 2, giving chance that there is no effect. This implies that for someone with 80 grade pitch recognition skills, they would perform half of a standard deviation better than everyone else, holding all else constant, which felt fair if this effect was actually present.  

Lastly, I let the standard deviation of the model have a prior of exponential with lambda = 1/12.  

![](/assets/photos/post4.png)  

This is a pretty good fit, especially compared to the more granular model using Reaction, Burst and Sprint Speed.  

![](/assets/photos/post5.png)  

As you can see, while I overestimated the effects of sprint speed and pitch recognition, my intuition was generally correct. A plus (one standard deviation) runner is expected have an Out% 7% higher than average, whereas a hitter with plus pitch recognition would only be expected to be 1% higher. Both are within the standard deviations I set earlier.  

Something that stands out here though is that the coefficient distributions for Reaction in the first model, and HPTR in the second model are fairly similar. HPTR has a bit wider distribution and thus gives some more credence to there being no effect, but both distributions are centered near 1, which means that in both models, having plus reaction skills results in just a 1% increase in Out%, holding all else constant.  

Now that we’ve gotten through the modeling, I wanted to apply this to the minor leagues, and see who has the highest predicted Out% using sprint speed and HPTR. As a reminder, the public doesn’t have access to sprint speed for minor leaguers, but earlier in the year, I wrote a post on how to estimate it.  

![](/assets/photos/post6.png)  

The players listed here are all in the Top 10 of the sprint speed leaderboards for 2024, unsurprisingly. What’s nice about this method is that it’s position agnostic, so while Caleb Ketchup primarily plays second base, we can estimate what his outfield Out% is based on non-fielding specific stats. This is helpful in deciding if we want to try having a player play the outfield even if he hasn’t played in the outfield before.  

In summary, pitch recognition has a modest effect on predicting outfield defense, at least by using Baseball Savant’s metrics. Raw speed and Burst are more important than pitch recognition, but both Reaction and our hitting proxy for Recognition, Hittable Pitches Taken, show a small positive effect on Out% for outfielders. A slow hitter won’t be a plus defender in the outfield if he has good pitch recognition at the plate, but I think for hitters who show elite pitch recognition, they should be given a chance to play the outfield, because there are transferrable skills from identifying if a pitch is hittable at the plate, and seeing the ball well off the bat in the outfield.  
