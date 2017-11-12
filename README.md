# TRANSIT: Toolbox foR ANcient Sailing tIme esTimation

## Short Description
Cost-surface analysis in Geographic Information System environment has been less frequently used in the study of ancient sail navigation than in other studies of the human past. Navigation cost-surface analysis entails the use of GIS tools that are versatile but not very easy to grasp and to put to work. `TRANSIT` is an *ArcGIS* toolbox built to facilitate cost-surface analysis of ancient sail navigation. It estimates the navigation time from a start location, considering parameters relevant for the generation of an accumulated anisotropic cost-surface, automating the complex workflow required to meaningfully pre- and post process the data. The toolbox may produce reasonable estimates. These should be thought of as values gravitating around, not matching, likely past durations. The estimated values may prove useful as an indication of the order of magnitude of past voyages’duration, and as frame of reference in measuring ancient maritime space through time.


### Reference
The rationale, use, and theoretical background for the use of the tool are fully described in:

Alberti G. 2017. *TRANSIT: a GIS toolbox for estimating the duration of ancient sail-powered navigation*. **Cartography and Geographic Information Science** (http://dx.doi.org/10.1080/15230406.2017.1403376)


## Data input
Data are fed into the toolbox via its **control panel**, which is depicted in the following figure. For easiness of use, each field is accompanied by a short help documentation.

[![TRANSIT_control_panel.png](https://s18.postimg.org/5qbqm96p5/TRANSIT_control_panel.png)](https://postimg.org/image/jwrhhhhk5/)

### Parameters
* `Wind speed`: raster representing the speed of the wind in the study area. The speed must be expressed in km/h.

* `Wind direction`: raster representing the direction of the wind in the study area. The direction must be expressed in degrees, following meteorological conventions (direction from which the wind is blowing).

* `Max scale value`: since a vessel cannot be as fast as the wind, its velocity must have an upper ceiling. The rescaling is performed using the following formula:
`Rescaled raster = [(grid - Min value from grid) * (Max scale value - Min scale value) / (Max value from grid - Min value from grid)] + Min scale value`,
where `grid` is the input wind speed raster, `Min` and `Max` scale value are the minimum and maximum value to the output rescaled raster. The `Min scale value` is automatically derived from the masked wind speed raster in order to honor the minimum value of the wind speed recorded within the study area. The `Max scale value` is entered by the user and depends on the maximum theoretical vessel velocity. The suggested value of `22.22` (in km/h, corresponding to `12 knots`) rests on reports in literature (see Alberti 2017).

* `Land mask`: raster representing the extent of the landmasses.The user may want to use a raster in which cells representing landmasses are given a `NoData` value. The wind speed and direction rasters are prepared by masking the landmasses with a `NoData` value. As part of the toolbox's workflow, ArcGIS will use `Raster Calculator` to perform a simple map algebra operation in which the wind speed and direction rasters are individually added to the landmass mask. Since, in map algebra, data plus `NoData` equals `NoData`, cells corresponding to the land will be given `NoData` and will be therefore excluded from the analysis.

* `Source location`: vector data (point shapefile) representing the start location, i.e. the location from which the accumulated cost-surface will be calculated.

* `Horizontal factor`: expresses the reduction or increase of cost according to whether the movement from a to b is aligned or not to a horizontal. The factor is defined by first assessing what is called `Horizontal Relative Moving Angle` (HRMA), i.e. the offset between the direction of the a-to-b movement and the horizontal flow. The latter represents the horizontal direction at each cell as an input raster. The `HRMA` ranges from 0 to 180 (inclusive) and is assumed symmetrical about 0; 180 degrees represent a movement opposite to the direction defined by the horizontal flow raster. Once the `HRMA` is defined, it is looked up in theuser-defined horizontal factor table and the corresponding horizontal factor is determined. A factor larger than 1 will increase the cost of moving, while the opposite holds true for a factor smaller than 1. The relation between the potential vessel performance and specific points of sail is used to work out a customized horizontal factor table (see: Alberti 2017).
Suggested horizontal factors for sail-powered navigation are: `1` for *running* (`0-34` degrees) and *beam reach* (`68-90` degrees); `0.42` for *broad reach* (`35-67` degrees); `2.5` for *close-hauled* (`91-113` degrees); `10` for *no go* (`114-180` degrees). The subjective factor of `10` dramatically inflates the time it takes to traverse each cell in the no-go direction. This makes sailing close-hauled comparatively less costly (i.e., more convenient) than moving directly against the direction of the wind. Users can customize the factors in the `HRM`A as they deem appropriate for their research questions.The figures between round brackets refer to the points of sail; angles follow the GIS convention for the `HRMA`. This convention reverses the nautical convention whereby wind is considered as coming from 0 degrees.
The user may want to use the drop-down menu to select acustomized table (in .txt format) whose content must be arranged in two columns with no headers: the first stores the `HRMA` values from 0 to 180 (inclusive), the second indicates the corresponding horizontal factor.

* `Output_folder`: the folder in which the user wants to save all the outputs of the analysis. The following files will be produced:
   + `wind_sp_msk` (masked wind speed raster)
   + `wind_dir_msk` (masked wind direction raster)
   + `w_sp_resc` (rescaled wind speed raster)
   + `sea_lev_msk` (masked sea level raster)
   + `pace_hkm` (pace raster, in h/km)
   + `accum_cost` (accumulated cost raster)
   + `acc_cst_adj` (adjusted accumulated cost raster)
   + `backl` (backlink raster)
   + `days` (adjusted accumulated cost expressed in days)
   + `1day_contours` (shapefile representing isochrones at 1-days intervals)
   + `3h_contours` (shapefile representing isochrones at 3-hours intervals)

The `backlink` raster can be fed (along with the adjusted accumulated cost raster) into the ArcGIS' `Cost Path` tool to calculate the sail-powered navigation least-cost path from the source location to destination locations.


## Installation
As shown below, the toolbox can be quite easily installed. It suffices o right-click on top of the ArcGIS’ toolbox tree, then select ‘add Toolbox’ from the pop-up menu, and then navigate through your PC folders to select the `TRANSIT.tbx' file.
