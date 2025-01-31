# Galaxy Properties and Evolution

## Introduction
The goal of this project was to investigate the properties and evolution of galaxies through a series of analyses, with a focus on understanding selection effects, galaxy structure, and the relationships between galaxy properties.

In [the Galaxy Morphology and Evolution project](https://github.com/juliarobe/Galaxy_Morphology_Evolution), I downloaded two samples of 5000 galaxies. The data in the samples contained the apparent magnitudes, scale radii, the axis ratio (a/b), redshift and more. Using this data, I computed the K-correction, the luminosity distance, the absolute magnitude, the surface brightness, and the physical size and for each galaxy.

For this project, I build upon my calculations from [the Galaxy Morphology and Evolution project](https://github.com/juliarobe/Galaxy_Morphology_Evolution) by accounting for the galaxies surveys selection effect, which just refers to the biases that arise from the way galaxies are selected for observation in a sample.

In [the Galaxy Morphology and Evolution project](https://github.com/juliarobe/Galaxy_Morphology_Evolution), I calculated and plotted the absolute magnitude, physical size, and surface brightness distributions. Here, I use the V-max technique to correct for selection effects, and compare the original observed distributions of absolute magnitudes, physical sizes and surface brightnesses to the distributions that are corrected for selection effects.

Next, I examine the observed 1D radial surface brightness of galaxies and find the best fit surface brightness profile for each galaxy's data. Galaxies generally exhibit an Exponential profile, deVaucouler profile, Sersic profile, or a combination of deVaucouler and Exponential profiles.

Then, using the data I downloaded in [the Galaxy Morphology and Evolution project](https://github.com/juliarobe/Galaxy_Morphology_Evolution), I compute the concentration index, which describes the distribution of light or mass within the galaxy and how concentrate or dispersed it is. I then bin galaxies in small absolute magnitude bins and plot the concentration index versus redshift.

Finally, I compute the median radius in both the r and g band in small bins of the evolution corrected absolute magnitudes in just the r-band.

## Part 1: Correcting for Selection Effects
### What is the V-max technique?
The V-max is the maximum volume within which a galaxy could be detected by a survey, given its sensitivity and selection criteria. To calculate the V-max, you need the apparent magnitude limit, which is the faintest magnitude that the survey can detect, the absolute magnitudes of the galaxies, the redshifts, the K-corrections, and the evolutionary correction, which is the correction for the change in the galaxy’s brightness over time. Using these variables, one can calculate the maximum distance (or redshift) at which the galaxy could be detected by the survey. This distance is then used to calculate the maximum volume (V-max) within which the galaxy could be detected. Once the V-max is computed, each galaxy in the sample can be weighted by its V-max value, which ensures that the galaxies that could be detected over a larger volume are given more weight in the analysis. By weighting each galaxy by its Vmax value, you can correct for the biases in the sample and obtain a more accurate representation of the galaxy population.

### Calculating V-max:
These are the steps I took to calculate V-max:
1. Calculate the corrected apparent magnitude (m_corr) and the absolute magnitude limit (Mlim)
2. Match the galaxies based on their observed magnitude (M_r) being less than the absolute magnitude limit
3. Calculate the maximum luminosity distance at which a galaxy could be detected by the survey
4. Calculate the volume limit (this is V-max!) using the maximum luminosity distance and the absolute magnitude limit

Now that I have V-max values, I can calculate the weight for each galaxy in the sample.

$$ 
weight_i = \frac{1}{V_{max, i}} 
$$


$$ 
weight_{normalized, i} = \frac{weight_i}{\Sigma \frac{1}{V_{max, total}}} 
$$

### Refining V-max calculation
To ensure the accuracy of V-max, I implemented a refinement step using an optimization technique. 

The refinement is based on the equation:

$$ 
m_{corrected}^{limit} = 17.5 - K_{corr}(z) + Q*z 
$$

First, I calculate the absolute magnitude limit $M_{lim}$ using the corrected magnitude limit $m_{corrected}^{limit}$ and the luminosity distance $d_L$, where:

$$ 
M_{lim} = m_{corrected}^{limit} - 5log(d_L) - 25 
$$

Next, I define a function fit_Q that calculates the volume limit using an interpolation function that takes the absolute magnitude values and finds the closest values in $M_{lim}$ to estimate the corresponding output values in V-max. The estimated output values are returned as $V_{lim, fit}$.

The funtion fit_Q calculates the difference between V-max and $V_{lim, fit}$. Then, the value of Q is adjusted to minimize the difference between V-max and $V_{lim, fit}$.

Finally, I use the optimized value of Q to recalculate V_max.

### Distribution plots:

<h2 style="text-align: center;">Absolute Magnitude Distribution: Observed vs V-max Corrected: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/abs_mag_dist_vmax.png" width="500">

For the absolute magnitude distribution, the V-max corrected distribution is more spread and shifted towards fainter, less luminous galaxies. Thus, we see that the original, uncorrected distribution was biased towards brighter galaxies.

<h2 style="text-align: center;">Physical Size Distribution: Observed vs V-max Corrected: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/size_dist_vmax.png" width="500">

For the physical size distribution, the V-max corrected distribution is shifted to the left towards smaller sizes, suggesting that the uncorrected distribution was biased towards larger galaxies. This makes sense since larger galaxies are easier to detect, especially at larger distances.

<h2 style="text-align: center;">Surface Brightness Distribution: Observed vs V-max Corrected: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/surf_brightness_dist_vmax.png" width="500">

For the surface brightness distribution, the corrected distribution has a similar median value as before, suggesting that the correction did not significantly affect the typical surface brightness distribution of galaxies. The slightly larger spread in the corrected distribution seems due to the inclusion of more faint or low-surface-brightness galaxies.

## Part 2: Best Fit 1D Radial Surface Brightness Profiles
For the following three galaxies, I did the following:
- Plotted their observed radial surface brightness profiles.
- Modeled the radial surface brightness profiles using the deVaucouleurs, exponential, Sersic, and deVaucouleurs + exponential models.
- Plotted the fitted profiles with the observed profiles.
- Calculated the best-fit radial surface brightness profiles with the non-linear least squares method.

<h2 style="text-align: center;">Galaxy #3 Profiles: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/gal3_R_vs_mu.png" width="500">

Although the best fit surface brightness profile is difficult to determine by eye, the least squares method tells me that the Sersic fit is the best fit.

<h2 style="text-align: center;">Galaxy #10 Profiles: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/gal10_R_vs_mu.png" width="500">

For this galaxy, the deVaucouleurs fit looks the closest to the observed data, although none of the profiles fit the data super well. Indeed, the least squares method tells me that the deVaucaoleurs fit is the best.

<h2 style="text-align: center;">Galaxy #14 Profiles: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/gal14_R_vs_mu.png" width="500">

For this galaxy, it seems like deVaucouleurs fit is the best fit, and the least squares method confirms this.

## Part 3: Concentration Index versus Redshift
### Concentration index:
Using the data I obtained during [the Galaxy Morphology and Evolution project](https://github.com/juliarobe/Galaxy_Morphology_Evolution), I computed the **concentration index** by dividing the radius at which the surface brightness of the galaxy falls to 90% of the sky brightness (the Petrosian radius, R90) by the radius at which the surface brightness of the galaxy falls to 50% of the sky brightness (the Petrosian radius, R50). The concentration index provides a measure of how concentrated the galaxy’s light is towards its center, since R90 typically encloses most of the galaxy’s light and R50 is often used as a measure of the galaxy’s half-light radius, which is the radius that encloses half of the galaxy’s total light. Therefore, a higher concentration index indicates that the galaxy’s light is more concentrated towards its center, while a lower concentration index indicates a more diffuse light distribution.

<h2 style="text-align: center;">Sample A Concentration Index versus Redshift: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/ci_vs_z_subplotsA.png" width="500">

<h2 style="text-align: center;">Sample B Concentration Index versus Redshift: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/ci_vs_z_subplotsB.png" width="500">

<h2 style="text-align: center;">Sample A Scatterplot of Concentration Index versus Redshift: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/ci_vs_z_single_scatterA.png" width="500">

<h2 style="text-align: center;">Sample B Scatterplot of Concentration Index versus Redshift: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/ci_vs_z_single_scatterB.png" width="500">

<h2 style="text-align: center;">Median Concentration Index versus Redshift: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/ci_vs_z_lineplotAandB.png" width="500">

Clearly, Sample A's galaxies have higher concentration indices than Sample B’s, indicating that Sample A’s galaxies have more centrally concentrated light profiles, whereas Sample B’s galaxies have more diffuse light profiles.

## Part 4: Median Radius versus Evolution Corrected Absolute Magnitude
Finally, I compute the median radius in both the r-band and the g-band in small bins of the evolution corrected r-band absolute magnitudes. Then, I plot the difference of the median values versus the evolution corrected r-band absolute magnitude.

<h2 style="text-align: center;">Median Radius vs Evolution Corrected Abs Magnitudes: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/Mg_Mr_vs_evolcorr_Mr.png" width="500">

<h2 style="text-align: center;">Difference of Median Radius vs Evolution Corrected Abs Magnitude: </h2>
<img class="img-fluid" style="display: block; margin: 0 auto;" src="./images/med_evolcorr_Mr.png" width="500">

The estimate for $R_g$ is slightly higher than that for $R_r$ at several points, indicating a systematic difference between the two bands. This discrepancy arises from the filter effect, where the observed flux in the g-band is affected by the redshift of the galaxies, causing the light to be shifted towards shorter wavelengths. To correct for this effect, a k-correction is necessary to convert the observed magnitudes to their respective rest-frame values. The k-correction involves calculating the flux in the rest-frame and observed-frame, taking into account the telescope’s filter response. By applying this correction, we can obtain a more accurate estimate of the galaxy sizes, unbiased by the effects of redshift. This correction is particularly important when comparing galaxy properties across different redshifts or bands.
