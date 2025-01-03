---
title:  "How Much Should Scouts Use Data?"
layout: post
---
As we are winding down on fall practices and heading towards winter with no games, I’ve started to think again about one of my favorite open-ended questions, which is how scouts and analytics interact with each other.  

I don’t have a ton of “old-school” beliefs, but one is that I think that scouts shouldn’t have access to more granular Trackman data. My reasoning comes from an analytical point of view; when you are building a model, you want to have predictors that are predictive, and you want them to be relatively uncorrelated to avoid multicollinearity. Obviously, Trackman data and scout grades should be fairly similar, but not perfect as the eye will see things differently than what the data shows. If your model consists of predictors are various data points from Trackman, along with scout grades, there shouldn’t be too much of a collinearity issue. If scouts are looking at Trackman while scouting the game, there’s a high risk that the scout grades will start mirroring what Trackman says, which ends up losing out on whatever uncorrelated signal the scout was providing. Sure, the eye test scout may have improved their prediction quality, but at the expense of the overall model performing worse.   

A counter that a friend brought up is that scouts are already bringing radar guns to the game to measure velocity, so what’s the difference between using just a radar gun versus looking at the iPad and seeing spin rate, break, etc. It’s a good point, and I don’t have a great counter argument to it other than I don’t think you should be glued to the radar gun either. Also, I think a lot of scouts want to see the data so they can get practice analyzing it themselves and improve their skillset/job security, which makes sense from a job security perspective and speaks to the scouts’ work ethic. But in the big picture, I question how effective this is.  

In short, I think the role of in person scouting is to pick up the residual signal that a model might not be accounting for. While it’s great that scouts are more open minded now about data, and I wouldn’t want to impede their personal development by learning more about analytics, I think that it’s important to think about the system as a whole. We don’t need our scouts to be more accurate than the model, we need them to be accurate in situations where a model might struggle. I think that skill goes away if you are having scouts look at data, which is why I lean against having your in-person scouts use data.   