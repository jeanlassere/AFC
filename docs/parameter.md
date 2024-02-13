## Defining system `parameter` input

The `parameter` input consists of settings used to characterize the building and its systems being optimized by the controller. The `parameter` object is a nested python dictionary containing subsidiary dictionaries, lists, or values. The structure and content of each of the components is outlined below:
- `controller`: General settings of the controller such as optimization horizon, timestep, and location of solvers.
- `facade`: Data used to describe the building and facade system on which the controller is implemented.
- `fuels`: Energy providing fuels within the model to power generators and other assets. This is typically not used in AFC.
- `objective`: Definition of the controller’s objective such as weights that are applied when constructing the optimization’s objective function.
- `occupant`: Configuration of occupant comfort and internal load demand settings.
- `radiance`: Configuration of the facade system.
- `site`: Site specific general characteristics, interconnection constraints, and regulation requirements.
- `solver_options`: Settings specific to the solver such as maximal solver time.
- `system`: System specific settings such as enabling or disabling other distributed energy resources or load assets.
- `tariff`: Tariff related energy and power rates. Note that tariff time periods are provided in the separate time-series input below.
- `wrapper`: Configuration to manage the execution of AFC.
- `zone`: Configuration of each control zone within the building.

## 1. Parameters that are in common with Dooper
The documentation for `system`, `controller`, `solver_options`, `objective`, `site`, `tariff` and `fuels` can be found in the dedicated [DOPER documentation](https://github.com/LBNL-ETA/DOPER/blob/master/docs/parameter.md).

## 2. Defining 'parameter['facade']'
This dictionary contains data needed to characterize the building and facade system:
- `convection_window_offset`: [float] Window convection quantity offset. Default value is 4.
- `convection_window_scale`: [float] Window convection quantity scale. Default value is 4.
- `fstate_initial`: [list] Initial state of each facade zone. Note: only available if the type is `'ec-71t'`. Default value is [3, 3, 3].
- `rad_cutoff`: [dict] Numeric low-end (first list element) and high-end (second list element) cutoffs for Radiance quantities:
  - `abs1`: [list] Solar absorption on outer window pane, in W. Default value is [50, 1e6].
  - `abs2`: [list] Solar absorption on inner window pane, in W. Default value is [5, 1e6].
  - `ev`: [list] Vertical illuminance at eye level, in lux. Default value is [250, 1e6].
  - `iflr`: [list] Transmitted and incident radiance on the floor surface, in W. Default value is [5, 1e6].
  - `shg`: [list] Solar heat gain, in W. Default value is [0, 1e6].
  - `tsol`: [list] Total transmitted radiance, in W. It includes `iflr`. Default value is [5, 1e6].
  - `wpi`: [list] Work plane illuminance, in lux. Default value is [5, 1e6].
- `states`: [list] List representing the possible states for each dynamic facade zone. Note: only available if the type is `'ec-71t'`. Default value is [0, 1, 2, 3].
- `type`: [str] Type of the facade. Note: currently only supports `'ec-71t'`.
- `windows`: [list] List of all window zones of the facade. Note: only available if the type is `'ec-71t'`. Default value is [0, 1, 2].
- `window_area`: [float] Total window area of the facade, in m2. Default value is 7.12.

## 3. Defining parameter['occupant']
This dict contains data about the occupant comfort expectations in the building zone:
- `equipment`: [float] Electric equipment load in room, in W. Note: only available if the zone type is `'single_office'`. Default value is 150.
- `glare_max`: [float] Maximum acceptable value for glare. Default value is 0.4.
- `occupancy_light`: [int] Occupancy light in room (unoccupied (0) or occupied (1)). Note: only available if the zone type is `'single_office'`. Default value is 1.
- `occupant_load`: [float] Thermal occupant load in room, in W. Note: only available if the zone type is `'single_office'`. Default value is 100.
- `plug_load`: [float] Thermal plug loads in room, in W. Note: only available if the zone type is `'single_office'`. Default value is 150.
- `schedule`: [str] Path to occupant schedule file to compute occupancy. 
- `temp_room_max`: [float] Maximum acceptable temperature in the building zone, in °C. Default value is 24.
- `temp_room_min`: [float] Minimum acceptable temperature in the building zone, in °C. Default value is 20.
- `wpi_min`: [float] Minimum work plane illuminance, in lux. Default value is 500.

## 4. Defining parameter['radiance']
This dict contains physical data needed to calculate the various dependencies (see above under `rad_cutoff` ) through Radiance:
- `dimensions`: [dict] Dimensions of the building as well as position and size of the window(s): 
  - `depth`: [float] Depth of the building, in m. Default value is 4.57.
  - `height`: [float] Height of the building, in m. Default value is 3.35.
  - `width`: [float] Width of the building, in m. Default value is 3.05.
  - `window1`: [str] Position of window 1 (bottom), in cartesian coordinates, in meters. X is the width, Y is the depth, and Z is the height.  Default value is '.38 .22 2.29 .85'. These values represent respectively: starting X, starting Y, width, and height. More specifically, the origin of x=0; y=0 is the ground level and the south facade is always at -y, north at +y, west at -x, and east at +x.
  - `window2`: [str] Position and size of window 2 (middle), in cartesian coordinates, in meters. X is the width, Y is the depth, and Z is the height. Default value is  '.38 1.07 2.29 .85'. These values represent respectively: starting X, starting Y, width, and height. More specifically, the origin of x=0; y=0 is the ground level and the south facade is always at -y, north at +y, west at -x, and east at +x.
  - `window3`: [str] Position and size of window 3 (top),in cartesian coordinates, in meters. X is the width, Y is the depth, and Z is the height.  Default value is '.38 1.98 2.29 .51'. These values represent respectively: starting X, starting Y, width, and height. More specifically, the origin of x=0; y=0 is the ground level and the south facade is always at -y, north at +y, west at -x, and east at +x.
- `elevation`: [float] Elevation of the building from sea level, in m. Default value is 100. 
- `location`: [dict] Data defining the location of the building:
  - `latitude`: [float] Latitude of the building, in Degree. Default value is 37.7. 
  - `longitude`: [float] Longitude of the building, in Degree. Default value is 122.2. 
  - `orientation`: [float] Orientation of the building, in Angular Degree. Note that 0 corresponds to South. Default value is 0.
  - `timezone`: [int] Timezone in which the building is located, in Hourly difference to GMT. Default value is 120. 
  - `view_orient`: [float] Orientation of the occupant inside the building, in Degree relative to the building orientation. For ‘orientation’ 0 deg (South) a ‘view_orient’ of -90 would correspond towards East. Default value is 0.
- `paths`: [dict] Internal paths to access resource files for the dynamic facade system required by Radiance:
  - `rad_bsdf`: [str] Path to the resources folder. For example: `'afc/resources/radiance/BSDFs'`.
  - `rad_config`: [str] Path to the radiance file. For example: `'afc/resources/radiance/room0.6WWR_ec.cfg'`.
  - `rad_mtx`: [str] Path to the matrices folder. For example: `'afc/resources/radiance/matrices/ec/0.6'`.
- `regenerate`: [bool] Flag to indicate if Radiance matrices should be regenerated. This is usually not necessary but can be forced through the flag. Default value is ‘False’.
- `wpi_loc`: [str] Location of the modeled work plane illuminance sensing in Radiance. Supported options are: 
  - `'23back'` Uses a virtual sensor located at the ⅔ in the back of the room.
  - `‘all’` Uses all virtual sensors in the room and takes the average.
  - `‘Center’` Uses a virtual sensor located at the center of the room.
- `wwr`: [float] Window to wall ratio of the building zone. Default value is 0.4.


