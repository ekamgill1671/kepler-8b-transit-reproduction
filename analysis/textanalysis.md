### ANALYSIS

## Intro
I analyzed KIC-6922244, a hot Jupiter exoplanet for star Kepler-8b. It is roughly 3,434 years away the teh constellation Lyra, and I used this object to detect planetary transits.

## First Steps
To begin looking whether or not this star had a transiting exoplanet, I first searched for the lightcurve and I downloaded it, as well as removing NaNs (not number values) and normalizing it. Normalizing the lightcurve is important, as it divides the flux values of a light curve by its median value. This scales the relative brightness data so that the median flux equals 1.0 (or percentage-based relative units), making it easier to analyze transit or variability signals, rather then having to look at large numbers. I holded off on removing outliers for now since I want to get to that later and how it can significantly impact how a lightcurve can look like if you aren't careful. 




