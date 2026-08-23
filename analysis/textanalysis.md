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

## Further Cleaning the Lightcurve

Understanding why transiting exoplanets rely on BLS rather then the Lomb-Scarlge method, I began to test out "folding" the lightcurve. Folding the Lightkurve stacks the light-curve cycles on top of one another using a specified orbital period. Here, I use the period with the highest power in the periodogram, approximately 3.520 days, because it is the period that best explains the repeating pattern in the data. Folding the lightkurve is useful because it converts the observation times into orbital phase using the approximately period found by BLS, measurements from different transits can be aligned. The individual transit signals then overlap, making the overall shape and depth easier to measure. I also use the transit time corresponding to the maximum BLS power, t0_bls, as the reference time when folding the light curve. This places the center of the detected transit at approximately phase 0. Folding converts the observation times into orbital phase, allowing measurements from different transits to be aligned. The individual transit signals then overlap, making the overall transit shape and depth easier to see and measure.

Because the planet blocks part of the star's visible surface, the measured flux decreases slightly during the transit. When the planet moves past the star, the flux returns to approximately its original level. The transit therefore appears in the light curve as a relatively short, repeated decrease in brightness. The time between these repeated transits gives information about the planet's orbital period, while the depth of the dip contains information about the relative sizes of the planet and its host star.

Before any processing, the raw light curve is dominated by stellar noise — slow brightness variations from things like star spots rotating in and out of view, instrumental drift, and other systematic effects that have nothing to do with the transiting planet. Against this noisy baseline, a transit signal as shallow as Kepler-8b's is difficult to pick out by eye; the dip is often smaller than the scatter in the surrounding data points.

Flattening removes these slow, large-scale trends by fitting and subtracting out the star's own long-term brightness variations, leaving behind a light curve centered around a flat baseline. Binning then averages groups of nearby data points together (using the `time_bin_size` discussed above), reducing point-to-point scatter without erasing the transit shape itself.

Applied together, flattening removes the slow trends that would otherwise obscure the transit, and binning suppresses the remaining short-term noise, which leaves a light curve where the transit dip on Kepler-8b stands out clearly against a flat, low-noise baseline, compared to the raw data where it's barely distinguishable from noise.

What happens when I plot the light curve? The resulting graph shows a clear, repeated dip in the brightness of KIC 6922244, the host star, which is consistent with a planet transiting in front of it. When I folded the light curve, Lightkurve used the orbital period I found earlier, 3.520 days, to convert each observation's time into an orbital phase. This lets measurements from different orbital cycles line up at the same point in the orbit and be compared directly, rather than staying spread out across the full multi-week observation window.

The result is a much clearer transit signal, since data from many individual cycles are effectively stacked on top of each other, averaging out noise that would otherwise obscure a single transit. The dip in normalized flux is consistent with Kepler-8b passing in front of its host star and briefly blocking a small fraction of its light.

The BLS search also returned a best-fit transit epoch, `t0_bls = 353.6118820626667`, which marks the reference time (in the same time system as the Kepler data, typically BKJD (Barycentric Kepler Julian Date)) of one specific transit center used to anchor the phase-folding. Every other transit in the dataset is phased relative to this value combined with the orbital period — get either one wrong, and the folded transits would land at slightly different phases instead of stacking cleanly, smearing out the same dip your binning analysis was trying to avoid. --double check this


But why should time_bin_size = 0.01? Why not a different number, and why is binning important? The parameter time_bin_size averages all the data points that fall within each time window (in days, since that's Lightkurve's default unit) to reduce noise and make the transit shape easier to see. A time_bin_size = 0.01 means you're averaging together every data point within a 0.01-day (~14.4 minute) window into a single point. I chose values for the time_bin_size ranging from 0.0001 all the way up to 1.0. As the number got larger, the stellar noise started to diminish. However, choosing a value that is too large like 1.0 smears out the shape of the transit. On the other hand, as the number gets smaller, the stellar noise becomes more prominent because most of the data points are barely averaging anything, keeping the stellar noise. So, a small bin sizes makes the transit shape eaiser to see, but at the cost of a noiser-looking light curve overall. Given Kepler-8b's transit duration of a few hours, I settled on 0.01 days (~14 minutes) as a middle ground since it is small enough to preserve the transit's shape, but large enough to reduce the stellar noise. 

Going along with this, why is the window_length equal to 401? What would happen if I chose different window_length values? Is there a difference? To test this, I flattened the light curve using four different window_length values: 101, 201, 401, and 801. The window_length controls the timescale over which Lightkurve estimates the underlying trend in the light curve. Smaller windows allow the flattening filter to respond to shorter-term variations, while larger windows produce a smoother estimate that primarily follows longer-term variations.

I then measured the transit depth for each flattened light curve using the same phase regions. Since I folded the light curve using the BLS-determined transit epoch, the center of the transit occurs at phase 0. I defined the central in-transit region as −0.02<phase<0.02 and the out-of-transit region as phase <−0.1 or phase>0.1. The median flux in each region was used to estimate the typical in-transit and out-of-transit brightness. 

| Window length | Transit depth |
| --------------||--------------|
|           101 |        0.585% |
|           201 |        0.775% |
|           401 |        0.819% |
|           801 |        0.860% |

The measured transit depth increased from 0.585% with a window length of 101 to 0.860% with a window length of 801. This corresponds to an increase of approximately 47% relative to the measurement using a window length of 101. These results show that the measured transit depth depends on the choice of flattening window.

As the window length increases, the estimated background trend becomes smoother and less responsive to short-timescale variations. As a result, the flattening process is less likely to follow the transit itself, leaving more of the transit signal intact and producing a deeper measured transit. In contrast, a smaller window is more responsive to short-timescale variations and can partially follow the transit when estimating the background trend. This can reduce the apparent depth of the transit in the flattened light curve.

This difference can be seen in the resulting light curves. The transit appears shallower with a window length of 101 and progressively deeper as the window length increases, with the deepest measured transit occurring at 801. This demonstrates that the choice of window_length can have a significant effect on the measured transit depth and is therefore an important source of uncertainty.

So, I selected a window_length of 401 as a compromise between the smaller and larger windows tested. It removes the slower variations in the light curve while retaining a clear transit signal. The comparison also demonstrates that the choice of detrending window is an important source of uncertainty in this analysis.








 
