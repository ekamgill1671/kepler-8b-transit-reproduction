# Objective

This project attempts to reproduce basic published properties
of the exoplanet Kepler-8b using publicly available Kepler
photometric data. This is purely for personal practice to obsere real astronomical data and lightcurves with exoplanets.

The primary goals are to:

- recover the orbital period using Box Least Squares (BLS)
- measure the transit depth
- estimate the planet-to-star radius ratio
- estimate the planet's radius
- compare the results with published values

# Data

Kepler target: KIC 6922244

Mission: Kepler

Quarter: 4

Cadence: Long cadence

Data source: MAST

The light curve was downloaded using the Python package
Lightkurve.

# Methods

The light curve was:

1. downloaded from MAST using Lightkurve
2. cleaned of NaN values
3. normalized
4. searched for periodic transit signals using BLS
5. folded using the best-fit orbital period

# Software

- Python
- NumPy
- Matplotlib
- pandas
- Astropy
- Lightkurve
