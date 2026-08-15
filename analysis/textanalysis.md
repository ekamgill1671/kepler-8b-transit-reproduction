### ANALYSIS

## Intro
I analyzed KIC-6922244, a hot Jupiter exoplanet for star Kepler-8b. It is roughly 3,434 years away the the constellation Lyra, and I used this object to detect planetary transits.

## Inital Light Curve
To begin looking whether or not this star had a transiting exoplanet, I first searched for the lightcurve and I downloaded it, as well as removing NaNs (not number values) and normalizing it. Normalizing the lightcurve is important, as it divides the flux values of a light curve by its median value. This scales the relative brightness data so that the median flux equals 1.0 (or percentage-based relative units), making it easier to analyze transit or variability signals, rather then having to look at large numbers. I holded off on removing outliers for now since I want to get to that later and how it can significantly impact how a lightcurve can look like if you aren't careful. 

## Creating the periodograms

A periodogram (for lightkurve) can calculate the power of different frequencies or periods in a stellar light curve, essentially allowing us to find repeating patterns, which is useful when trying to find variable stars, exoplanets, etc.

However, it is important to note that there are two methods astronomers can use for a periodogram: Box Least Squares Method (BLS) or Lomb-Scragle. Before I dive into the definition, let's see what happens when I use each method! When I used the lomb-scargle method, I noticed that it peaks at approximately 1.7600 days, while the BLS peaks at around 3.5200, which I also printed in the terminal. I can see that the lomb-scargle periodogram also has a lot of strange peaks, not showing a consistent "high peak" like the BLS.

The Lomb–Scargle method asks, “At what frequency does a sinusoidal model best fit the data?” It tests different frequencies and measures how well a sinusoidal model explains the observations at each one. A higher peak indicates stronger evidence for a periodic signal at that frequency. Because Lomb–Scargle searches for sinusoidal variations rather than transit-shaped dips, it can produce multiple prominent peaks from harmonics of the underlying signal, causing it to be hard to figure out when the planet transits. 

However, the box least sqaures method is also an algorthium using to find box-shaped dips (repeating) in a star's birghtness over time. It tests multiple trial orbital periods, phase-folding the lightcurve over that period, and fitting an upside-down "box" model to find periodic planetary transits. If the a "trial" period (period that is being tested by the BLS) can be matched with the true orbital period, the transit will produce a strong spike on the periodogram, which is why there is a huge peak at 3.5200 days (the period).

So, the reason the BLS method is used rather than lomb-scargle as transiting exoplanets create a flat-bottomed, abrupt dip, and the BLS matches this geometric shape. The Lomb-Scargle periodogram looks for wave-like (also known as sinusoidal variations), which are better for finding variable stars. 

To conclude, that is why I am going to use the BLS method for the rest of the data that I will be analyzing.

*need to explain period_at_max_power

## Folding the lightcurve

Understanding why transiting exoplanets rely on BLS rather then the Lomb-Scarlge method, I began to test out "folding" the lightcurve. Folding the Lightkurve stacks the light-curve cycles on top of one another using a specified orbital period. Here, I use the period with the highest power in the periodogram, approximately 3.520 days, because it is the period that best explains the repeating pattern in the data. By folding the data at this period, observations from different orbital cycles line up, making the repeated transit easier to see and allowing me to analyze its shape and depth more clearly. 

What happens when I plot the light curve? The resulting graph shows a clear, repeated dip in the brightness of exoplanet KIC 6922244, which is consistent with a transit. When I folded the light curve, Lightkurve used the orbital period I found earlier, 3.520 days, to transform the observation times into orbital phase. This allows measurements from different orbital cycles to be placed at the same phase and compared with one another. The result is a much clearer view of the transit because observations from multiple cycles are effectively stacked together. The decrease in normalized flux is consistent with a planet passing in front of its host star (Kepler 8-b) and blocking a small amount of starlight.







 
