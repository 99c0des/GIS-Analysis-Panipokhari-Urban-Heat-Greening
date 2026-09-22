# Urban Vegetation & Heat Exposure Analysis --- Panipokhari, Kathmandu

## Overview

This project uses Landsat 9 remote sensing data and QGIS/Python to
identify and spatially prioritize areas for urban greening based on
vegetation condition, built-up intensity, and land surface temperature.

The workflow combines NDVI, NDBI, and Land Surface Temperature (LST)
into vegetation, built-up, heat, greening-priority, and final
urban-priority classes.

## Objectives

1.  Assess vegetation conditions using NDVI.
2.  Identify relative built-up intensity using NDBI.
3.  Estimate land surface temperature from Landsat 9 thermal data.
4.  Classify vegetation, built-up intensity, and heat exposure.
5.  Combine these factors into a greening-priority and urban-priority
    framework.
6.  Produce a final spatial priority map.
7.  Calculate the area represented by each priority class.

## Data & Tools

**Satellite:** Landsat 9 Collection 2 Level-2

Key inputs: - Red reflectance - Near Infrared (NIR) - Shortwave Infrared
(SWIR) - `ST_B10` - QA/SCL products for quality control

**Software:** QGIS 4.2.2, Python 3.12, Rasterio, NumPy, GeoPandas, GDAL,
and PROJ.

## Methodology

### 1. NDVI --- Vegetation Condition

``` text
NDVI = (NIR - Red) / (NIR + Red)
```

  NDVI              Vegetation Class
  --------------- ------------------
  `< 0.20`                         1
  `0.20 – 0.40`                    2
  `> 0.40`                         3

The reconstructed classification matched the existing vegetation raster
by 100%.

### 2. NDBI --- Built-up Intensity

``` text
NDBI = (SWIR - NIR) / (SWIR + NIR)
```

  NDBI                  Built-up Class
  ------------------- ----------------
  `< 0.0572`                         1
  `0.0572 – 0.1057`                  2
  `>= 0.1057`                        3

Verification: 29,895/29,895 pixels matched (100%).

### 3. Land Surface Temperature

Landsat 9 `ST_B10` was converted to Celsius using:

``` text
LST (°C) = (DN × 0.00341802 + 149.0) - 273.15
```

Panipokhari LST: - Minimum: 31.85 °C - Maximum: 40.61 °C - Mean: 37.61
°C

The reconstructed LST matched the existing raster to within
approximately 0.00003 °C.

### 4. Heat Classification

  LST                      Heat Class
  ---------------------- ------------
  `< 37.457 °C`                     1
  `37.457 – 38.352 °C`              2
  `>= 38.352 °C`                    3

Verification: 100% pixel match.

### 5. Greening Priority

The verified raster-equivalent rule was:

``` text
Greening Priority = (4 - Vegetation Class) × Built-up Class
```

    Vegetation   Built-up   Greening Priority
  ------------ ---------- -------------------
             3          1                   1
             3          2                   2
             3          3                   3
             2          1                   2
             2          2                   4
             2          3                   6
             1          1                   3
             1          2                   6
             1          3                   9

Verification: 100% pixel match.

> These are raster-equivalent reconstruction rules. They reproduce the
> existing outputs exactly but should not be interpreted as recovery of
> the original source code.

### 6. Urban Score

Base score:

    Green   Built-up   Base Urban Score
  ------- ---------- ------------------
        1          1                1.0
        2          1                1.4
        2          2                1.3
        3          1                1.8
        3          3                1.6
        4          2                1.7
        6          2                2.1
        6          3                2.0
        9          3                2.4

Heat adjustment:

``` text
Urban Score = Base Urban Score + 0.3 × (Heat Class - 1)
```

The reconstructed urban score agreed with the existing output to
floating-point precision.

### 7. Final Priority

After rounding the urban score to one decimal:

  Rounded Urban Score   Final Priority
  --------------------- ----------------
  `<= 1.6`              1 --- High
  `1.7 – 2.4`           2 --- Medium
  `>= 2.6`              3 --- Low

The reconstructed final-priority raster was pixel-for-pixel identical to
the existing final raster.

## Workflow

``` text
Landsat 9 Level-2
      │
      ├── Red + NIR → NDVI → Vegetation Class
      │
      ├── NIR + SWIR → NDBI → Built-up Class
      │
      └── ST_B10 → LST → Heat Class
                         │
        Vegetation + Built-up
                         ↓
                 Greening Priority
                         │
                         └──────┐
                                ↓
                           Urban Score
                                ↓
                         Final Priority
                                ↓
                       High / Medium / Low
                                ↓
                         Area & Map Analysis
```

## Results

Based on the dissolved priority polygons:

  Priority             Area (m²)   Area (km²)      Share
  ----------- ------------------ ------------ ----------
  High                782,499.56       0.7825     26.84%
  Medium            1,441,784.89       1.4418     49.45%
  Low                 691,488.03       0.6915     23.72%
  **Total**     **2,915,772.48**   **2.9158**   **100%**

The largest mapped category is Medium Priority, representing
approximately half of the mapped priority-polygon area.

## Quality Control

The analytical workflow was checked by reconstructing the classification
and scoring relationships from the resulting raster outputs.

-   Vegetation classification: 100% pixel match
-   Built-up classification: 29,895/29,895 pixels matched
-   Heat classification: 100% pixel match
-   Greening priority: 100% pixel match
-   Urban score: agreement to floating-point precision
-   Final priority: 100% pixel-for-pixel match

## Limitations

-   NDVI, NDBI, and LST are remote-sensing indicators and do not
    directly measure every aspect of urban environmental conditions.
-   LST represents land-surface thermal conditions rather than
    near-surface air temperature.
-   Classification thresholds and scoring weights influence the
    resulting priority map.
-   The analysis represents the selected Landsat acquisition rather than
    a continuous time series.
-   The scoring framework is a spatial prioritization model, not a
    prediction of future urban conditions.
-   The documented scoring rules are verified raster-equivalent
    reconstructions of the produced outputs.

## Key Outputs

-   NDVI raster
-   Vegetation classification
-   NDBI raster
-   Built-up classification
-   Land Surface Temperature raster
-   Heat classification
-   Greening-priority raster
-   Urban-score raster
-   Final priority raster
-   Dissolved priority polygons
-   Priority area statistics
-   Final QGIS map

## Skills Demonstrated

Remote sensing · Raster analysis · NDVI · NDBI · Land Surface
Temperature · QGIS · Python · Rasterio · NumPy · GDAL · Spatial analysis
· Multi-criteria analysis · Cartography · GIS quality control

## Author

**Gunjan Gautam**\
Environmental Science \| GIS & Remote Sensing | 2026

