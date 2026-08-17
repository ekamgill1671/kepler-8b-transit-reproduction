### ANALYSIS

## Intro
I analyzed KIC-6922244, a hot Jupiter exoplanet for star Kepler-8b. It is roughly 3,434 years away the the constellation Lyra, and I used this object to detect planetary transits.

## Inital Light Curve
To begin looking whether or not this star had a transiting exoplanet, I first searched for the lightcurve and I downloaded it, as well as removing NaNs (not number values) and normalizing it. Normalizing the lightcurve is important, as it divides the flux values of a light curve by its median value. This scales the relative brightness data so that the median flux equals 1.0 (or percentage-based relative units), making it easier to analyze transit or variability signals, rather then having to look at large numbers. I holded off on removing outliers for now since I want to get to that later and how it can significantly impact how a lightcurve can look like if you aren't careful. 

I also am using the remove_outliers() function on the lightcurve in order to clean my data by removing things like telemtry glitches or harsh cosmic rays. "Sigma" defines the number of standard deviations a data point's value of flux can be from the median until it becomes classified as an outlier and is deleted from the graph. Before I chose what value sigma could be, I looked at the initial lightcurve, and when I measured the noise, the main band of data (the stellar noise) has a total thickness from about 0.998 to 1.002. This means the standard deviation (σ) is roughly 0.001. As I measured the spikes, I noticed there are positive flare-like spikes shooting up to 1.010 and 1.012. I calculated the distance: A spike at 1.010 is 0.010 away from the center line (1.000). Divide that by the noise (0.010 / 0.001), and I got 10 sigma. The Goal: I set sigma=8 or 9 since it the sweet spot because it is just below the height of those huge cosmic ray or flare spikes (deleting them), but safely above the normal stellar noise band. Going below the values 8 or 9 make it so that it slow deletes points where the planet transits, as those points become to be seen as "outliers". Using a low sigma (e.g., sigma=3) will cause Lightkurve the transit dip are "bad" data, deleting those points and completly erasing the planet's signal. In "data" you can see how a lightcurve will be impossible to read and try to find transits because it skews the data and prevents any viewings of a possibly orbital phase or signs of a transit. It is important to choose the best sigma value to keep the integrity of a graph/light curve.

Alternatively, one can also use sigma_upper and sigma_lower, which splits the sigma control into two indepdent thrsholds. Sigma_upper will set the threshold for positive spikes pointing upwards (e.g. cosmic rays, stelar flares, etc.). Sigma_lower will set the threshold for negative drops pointing downward (e.g. planetary transits, eclispes). Ideally, it would be the best practice to clean the top spikes aggressively, but protect the bottom transits. For now, I am going to use sigma=9, as it is giving me the cleanest results. 

Looking at the initial lightcurve, we can tell there is a pattern with the dips in the flux. However, right now it is difficult to determine the orbital period for the planet transiting Kepler-8b. There is still some digging that needs to be done for the light curves. 

## Creating the periodograms

A periodogram (for lightkurve) can calculate the power of different frequencies or periods in a stellar light curve, essentially allowing us to find repeating patterns, which is useful when trying to find variable stars, exoplanets, etc.

However, it is important to note that there are two methods astronomers can use for a periodogram: Box Least Squares Method (BLS) or Lomb-Scragle. Before I dive into the definition, let's see what happens when I use each method! When I used the lomb-scargle method, I noticed that it peaks at approximately 1.7600 days, while the BLS peaks at around 3.5200, which I also printed in the terminal. I can see that the lomb-scargle periodogram also has a lot of strange peaks, not showing a consistent "high peak" like the BLS.

The Lomb–Scargle method asks, “At what frequency does a sinusoidal model best fit the data?” It tests different frequencies and measures how well a sinusoidal model explains the observations at each one. A higher peak indicates stronger evidence for a periodic signal at that frequency. Because Lomb–Scargle searches for sinusoidal variations rather than transit-shaped dips, it can produce multiple prominent peaks from harmonics of the underlying signal, causing it to be hard to figure out when the planet transits. 

However, the box least sqaures method is also an algorthium using to find box-shaped dips (repeating) in a star's birghtness over time. It tests multiple trial orbital periods, phase-folding the lightcurve over that period, and fitting an upside-down "box" model to find periodic planetary transits. If the a "trial" period (period that is being tested by the BLS) can be matched with the true orbital period, the transit will produce a strong spike on the periodogram, which is why there is a huge peak at 3.5200 days (the period).

So, the reason the BLS method is used rather than lomb-scargle as transiting exoplanets create a flat-bottomed, abrupt dip, and the BLS matches this geometric shape. The Lomb-Scargle periodogram looks for wave-like (also known as sinusoidal variations), which are better for finding variable stars. 

To conclude, that is why I am going to use the BLS method for the rest of the data that I will be analyzing.

*need to explain period_at_max_power

## Further Cleaning the Lightcurve

Understanding why transiting exoplanets rely on BLS rather then the Lomb-Scarlge method, I began to test out "folding" the lightcurve. Folding the Lightkurve stacks the light-curve cycles on top of one another using a specified orbital period. Here, I use the period with the highest power in the periodogram, approximately 3.520 days, because it is the period that best explains the repeating pattern in the data. By folding the data at this period, observations from different orbital cycles line up, making the repeated transit easier to see and allowing me to analyze its shape and depth more clearly. 

What happens when I plot the light curve? The resulting graph shows a clear, repeated dip in the brightness of exoplanet KIC 6922244, which is consistent with a transit. When I folded the light curve, Lightkurve used the orbital period I found earlier, 3.520 days, to transform the observation times into orbital phase. This allows measurements from different orbital cycles to be placed at the same phase and compared with one another. The result is a much clearer view of the transit because observations from multiple cycles are effectively stacked together. The decrease in normalized flux is consistent with a planet passing in front of its host star (Kepler 8-b) and blocking a small amount of starlight.

However, the light curve can still be cleaner. That is why I used Lightkurve's flatten() function with a window_length of 401 to remove slow, low-frequency variations in the stellar light curve while preserving the planet's transit signal. The window_length determines the timescale over which the underlying trend is estimated. A value of 401 provides a relatively broad smoothing window, allowing the function to remove gradual changes in the light curve without following the short-duration planetary transit too closely. This helps keep the transit depth and shape visible for further analysis. 

After folding the light curve, I binned the measurements using a time_bin_size of 0.01. This combines nearby measurements and calculates a representative median flux, reducing random scatter while retaining enough resolution to preserve the shape of the transit.

Comparing the original and processed light curves shows that folding and binning make the repeating transit signal much easier to identify. The individual measurements in the original light curve contain substantial scatter and long-term variations, whereas the folded and binned light curve combines observations from multiple orbital cycles into a clearer representation of the transit.

But what would happen if I chose different window_lengths? Is there a differnece? 



 
