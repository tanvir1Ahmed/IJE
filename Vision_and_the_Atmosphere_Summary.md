# Summary: Vision and the Atmosphere

A summary of the report [`Vision_and_the_Atmosphere_Report.pdf`](Vision_and_the_Atmosphere_Report.pdf), which reviews:

> Narasimhan, S.G. & Nayar, S.K. (2002). *Vision and the Atmosphere.* International Journal of Computer Vision 48(3), 233–254. Columbia University.

## Core idea

Fog and haze scatter light more the farther it travels. Bad weather therefore encodes scene depth. Instead of treating weather only as a nuisance, the paper inverts this effect to recover 3D structure and clear-day colors.

## The problem

- Most computer vision algorithms assume the camera sees through perfectly clear air.
- Generic tools such as histogram equalization and contrast stretching apply the same correction everywhere. They cannot undo damage that grows with distance.
- Earlier physics-based methods each depended on outside information:
  - Cozman & Krotkov (1997) assumed every scene point has the same clear-day brightness.
  - Oakley & Satherley (1998) needed separately measured range data.
  - Kopeika and Yitzhaky et al. (1998) needed weather forecasts and a prior distance estimate.

The authors asked whether the weather itself could supply the missing information.

## Models

The method is physics-based, with no training data.

| Model | Equation | What it explains |
|---|---|---|
| Attenuation (Bouguer, Allard) | E(d, λ) = E₀(λ) e^(−β(λ)d) | Objects grow dimmer with distance |
| Airlight (Koschmieder) | L(d, λ) = L∞(λ)(1 − e^(−β(λ)d)) | Distant objects look brighter and washed out |
| Wavelength dependence | β(λ) ∝ 1/λ^γ, γ ≈ 0 for fog and haze | Fog scattering is nearly the same for every color (confirmed with MODTRAN) |
| Dichromatic atmospheric scattering (new) | E = pD + qA | A pixel's colors under different weather all lie on one plane |

In the new model, D is the direction of the point's clear-day color and A is the direction of the fog color. Neither changes with the weather, so the geometry of the plane gives:

- the fog color, without seeing the sky
- groups of scene points at equal depth
- a full depth map, up to scale
- the horizon brightness, from non-sky pixels
- the clear-day colors of the whole scene

## Problems solved

1. Relative depths of light sources at night (two night images)
2. Scene structure from airlight (one foggy image with some horizon visible)
3. Separating direct light from airlight (one clear and one foggy image)
4. Fog or haze color without the sky (two bad-weather images)
5. Segmenting the scene into equal-depth regions (two bad-weather images)
6. Complete scene structure (two bad-weather images)
7. Clear-day colors, that is, defogging (two bad-weather images)

## Data

The paper uses no public dataset. Experiments use the authors' own film photographs of New York scenes, plus MODTRAN simulations and a synthetic 16-patch image. The closest public dataset is the Columbia [WILD dataset](https://www.cs.columbia.edu/CAVE/software/wild/index.php), about 3,000 calibrated images of one urban scene. It is available on request for research use only.

## Results

- **Synthetic test:** depth recovery is exact without noise. Error rises to 15–18% at the highest noise level and is worse when the two fog levels are similar.
- **Plane fit on real images:** mean error of 0.25° (fog) and 0.31° (haze) over about 500,000 pixels.
- **Fog color without the sky:** within 1.2–1.6° of the color measured from the sky.
- **Ground-truth depth ratios:** mean absolute error of about 5.6% (the report's own calculation from the paper's table).
- **Comparison with earlier methods:** no numerical head-to-head. The gain is in what is no longer required: range data, forecasts, scene assumptions or a visible sky.

## Limitations

**Stated by the authors**

- Covers fog and haze only, not rain, snow, hail or clouds.
- Assumes scattering does not change with color and ignores multiple scattering and blur.
- Assumes the air is evenly lit. Buildings that block the sky cause depth errors.
- Fails where a scene point has the same color as the fog.
- Needs two images of a static scene and careful radiometric calibration.
- Recovers relative depth only, not absolute distance.

**Added by the report**

- Few quantitative real-world results and only one ground-truth table.
- A possible error in Equation 54 of Appendix B. It does not affect the main algorithms.
- The 1/d² term does not strictly apply to extended surfaces, though it cancels in every ratio the algorithms use.
- A small, specific test set of a few New York scenes under overcast skies.

## Conclusion

The paper turns bad weather from a nuisance into a source of information. Using classical attenuation and airlight physics plus a new dichromatic model, it recovers depth, fog color and clear-day colors from one or two bad-weather images, without range sensors, forecasts or scene priors. Its main weaknesses are the need for multiple images of a static scene, the restriction to fog and haze, and limited quantitative evaluation. It became a foundation for later dehazing research.
