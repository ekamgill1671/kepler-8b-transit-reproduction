### ANALYSIS

## Intro
I analyzed KIC-6922244, a hot Jupiter exoplanet for star Kepler-8b. It is roughly 3,434 years away the the constellation Lyra, and I used this object to detect planetary transits.

## Inital Light Curve
To begin looking whether or not this star had a transiting exoplanet, I first searched for the lightcurve and I downloaded it, as well as removing NaNs (not number values) and normalizing it. Normalizing the lightcurve is important, as it divides the flux values of a light curve by its median value. This scales the relative brightness data so that the median flux equals 1.0 (or percentage-based relative units), making it easier to analyze transit or variability signals, rather then having to look at large numbers. I holded off on removing outliers for now since I want to get to that later and how it can significantly impact how a lightcurve can look like if you aren't careful. 

## Creating the periodograms

A periodogram (for lightkurve) can calculate the power of different frequencies or periods in a stellar light curve, essentially allowing us to find repeating patterns where is useful when trying to find variable stars, exoplanets, etc.

However, it is important to note that there are two methods we can use for a periodogram: Box Least Squares Method (bls) or Lomb-Scragle. Before we dive into the definition, let's see what happens when we use each! When we used the lomb-scargle method, we see that it peaks at approximately 1.7600 days, while the BLS peaks at around 3.5200, which we also printed in the terminal. We can see that the lomb-scargle periodogram also has a lot of smaller, strange peaks before approaching the biggest peak 1.7600. 

This is because the lomb-scragle method is asking: "At what period does a sinusoidal model fit the data best?" Lomb-scargle tries different frquencies and tries to find how well a sinusoidla model can explan a certain obseration at this frequency. Per frequency, it calculates a measure of how much better that periodic model explains the data. If there is a high peak, it is stating that there is strong evidence that a periodic sinusodial component exists around this frequency. This is why in our image we have multiple high peaks instead of one consistent one like a planet does when it transits. 






