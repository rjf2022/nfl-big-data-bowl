# Speed Matters: How Defender 40 Times Impact Pass Defense

## Introduction

The NFL Combine is where the league's top prospects put their athleticism on display, showcasing their speed, agility, and strength. One of the most iconic tests is the 40-yard dash — a baseline measure of a player's straight-line speed and acceleration. But how much does this actually matter once players hit the field? 

In this analysis, we examine how a defender's 40-yard dash time influences their ability to close on the ball during pass plays and how teams can use these insights to sharpen their play calling. 

## Data Sources 

### Player Tracking Data 

Through the NFL Big Data Bowl, we were provided player tracking data from the 2023 NFL season. This included frame-by-frame receivers and defenders on pass plays before the pass and while the ball was in the air. It also included supplementary info about each play such as the pass result, route of the targeted receiver, and defensive coverage type. 

These are a few terms we'll use with the tracking data: 

- Final frame - last frame in the dataset, signifying when the ball is caught, falls incomplete, or gets intercepted, 
- Ball landing position - position of the ball at the final frame (so if the ball is caught, this is actually the spot of the catch)
- Primary defender - defender that's closest to the targeted receiver at the final frame 

### Defender 40-Times 

We pulled NFL Combine results from this [Pro Football Reference](https://www.pro-football-reference.com/) website to get the 40 times for each defender. Note that this only uses 40 times from the NFL Combine, so we don't have data for defenders who didn't run the 40-Yard Dash in the Combine, and they were excluded from the analysis. 

## Defender Tracking Metrics 

Using the tracking data, we developed 3 different metrics to measure the defender's movement throughout the play: 

1. Top Speed - max speed of the defender throughout the play 
    - This includes both before the ball and thrown and while the ball is in the air 
    - Measured in miles per hour (mph)
2. Peak Acceleration to the Ball - max acceleration towards the ball landing position 
    - We calculated this using distance between the defender's position at each frame and the ball landing position (so that it takes into account the defender's ability to change direction to move towards the ball) 
    - This is measured in yards per second squared (yd/s²) 
3. Final Separation - distance between the receiver and primary defender at the final frame 
    - Measured in yards 

We picked out a few play examples below to demonstrate these metrics: 

### Example 1. DJ Turner vs Go Route 

In the first example, we see DJ Turner (who set the record for the 2023 Combine with a 4.26) having to turn and run with the receiver on a go route (allowing only ___ yards of separation by the time the ball gets there): 

(Figure 1: example 1 play screenshot) 

When we plot out his speed throughout the play, we see that he hit a top speed of 22 mph (which is actually faster than the receiver), allowing him to catch up to the receiver and prevent a big play. 

(Figure 2: example 1 chart) 

### Example 2. Deonte Banks vs Out Route 

In our second example, the receiver runs a deep out in the end zone, forcing Deonte Banks to shift from a slight backpedal to a sprint towards the sideline: 

(Figure 3: example 2 play) 

When we plot out his acceleration to the ball, we see that it peaks at 7.5 yd/s² - allowing him to catch up to the receiver (allowing only __ yards of separation at the end), get out in front, and prevent the touchdown. 

(figure 4: example 2 chart)

## High-Impact Setups 

While defender speed had a big impact on the examples above, this isn't always the case. There are a lot of factors that go into whether or not a pass is completed, so speed alone doesn't always have a significant impact. Thus, we created a simple ML model to estimate the actual impact of defender speed on pass plays. 

### ML Model 

We built a model that predicts whether or not a pass will be completed based on 3 simple factors: 

- The 40 time of the primary defender 
- The route of the targeted receiver 
- The defensive coverage type 

Note that we used an XGBoost tree model where the first 13 weeks of the season were used to train the model (i.e. "training dataset") and the last 5 weeks were used to evaluate model accuracy (i.e. "test dataset"). 

### SHAP Values 

We used a tool called SHAP values to measure the effect that defender 40 time has on each prediction. We can calculate a SHAP value for each of our 3 predictor variables (on a specific play) that tells us how influential each variable is on the completion probability. 

We won't go into too much detail about SHAP values, but there are 2 main guidelines for how to interpret the 40 time impact on a given play: 

1. A value of 0 means no significant impact 
2. The higher the value, the bigger the impact 

### High-Impact Classification 

To simplify our analysis, we decided to flag "high-impact" plays where the primary defender has an elite 40 time that actually has a significant impact on the play. Note that we classified an "elite" 40 time as less than __ seconds and a "significant" impact as a SHAP value above __. 

For example, in the play below (see Figure 5), we see a ____ route planned against a ____ defense. This route will likely be covered by ____. 

Against a normal defender (i.e. average 40 time), we estimate a __% completion probability for this route. However, against ____, the completion probability drops down to only __%. 

(Figure 5: play prediction)

In an actual game scenario, we could setup this model to evaluate the play setup in real-time, send alerts for when high-impact setups occur, and prompt the players/coaches to adjust the play design to avoid the elite defender. 

## Overall Trends 

In addition to flagging setups in real-time, we can look at a few overall trends to analyze the general impact of defender speed. To start, we'll combine our "high-impact" classification with the defender tracking metrics from before. 

Overall (in the test dataset), we found that in high-impact plays, defenders 

- Move at a **_% higher top speed** (__ mph vs __ mph) 
- Accelerate to the ball **_% harder** (__yd/s² vs __yd/s²) 
- Allow **_% less separation** (__ yards vs __ yards) 

Ultimately, these lead to a **_% lower completion rate** (_% vs _%). 

### Breakout by Route 

To see which routes are most impacted by defender speed, we summarized the high-impact plays across the different route types: 

(Table 1: high-impact plays by route) 

We see here that defender speed has the highest impact on __ routes, but not much of an impact on __ routes. 

### Breakout by Coverage 

We also broke it out accross the different defensive coverage types to see which coverage schemes are most impacted by defender speed: 

(Table 2: high-impact plays by coverage type)

We see here that defender speed is most important in __ coverages, but not as important in __ coverages. 

## Conclusion

At the end of the day, the 40-yard dash is more than just a combine headline — it can have a huge impact on the right play. While defender speed is just one of many factors, we used Data Science to identify scenarios where it's most important and compared it to in-game speed metrics from player tracking data. 

By building a predictive model and identifying "high-impact" setups, we've created a framework that allows teams to:

1. **Evaluate matchups in real-time** — Identifying when an elite defender's speed gives them a significant advantage
2. **Adjust play calling** — Modifying route designs or targeting decisions when facing high-impact matchups
3. **Optimize game planning** — Understanding which routes and coverages are most vulnerable to elite defender speed 

This approach transforms combine metrics from simple scouting data into actionable intelligence that can influence in-game decision making. As tracking data continues to improve and the analytics become more sophisticated, it's insights like these that'll help teams survive and thrive in the modern NFL. 

## Appendix: Code

The code for the analysis and visuals is available in a public GitHub repo. The code is broken out across a few different Jupyter notebooks: 

1. Summarize Tracking Data 
2. Pull Combine Data 
3. Analyze Metrics 
4. Create Visuals 

## Acknowledgments

This analysis was conducted as part of the 2026 NFL Big Data Bowl using player tracking data provided by the NFL. Special thanks to the NFL for making this rich dataset available to the analytics community.

Michael Lopez, Tom Bliss, Ally Blake, and Addison Howard. NFL Big Data Bowl 2026 - Analytics. https://kaggle.com/competitions/nfl-big-data-bowl-2026-analytics, 2025. Kaggle.
