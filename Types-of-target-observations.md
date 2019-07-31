Depending on the science observation, an observation method is selected from the available observation types.
The most standard observation type is to _`track`_ a target during observation, but a number of _`scan`_ type observation that is used to move across a target rather than remain on it is also available.

## Standard observation types

### Tracking a target
Stay on target for the duration of the observation by tracking the target in time.   
Observation input requires a _`target`_ specified as a _`katpoint.Target`_ object, as well as the _`duration`_ to track the target in seconds.


### Performing a drift scan
Track a constant point in front of the target on the expected trajectory, allowing the object to _`drift`_ over the observation point. This generally results in a map of the observed beam pattern.    
Similar to the _`track`_ observation, a _`target`_ and _`duration`_ is required as input to this observation type.

## Specialised scan observation types
Scanning across a target requires a projection correction in order to achieve the expected outcome.
The forward projections transform spherical coordinates into planar coordinates.

| Projection | Tag | Description |
| --- | --- | --- |
| orthographic  | SIN  | An orthographic projection projecting the celestial sphere onto a tangent plane, but the centre of projection is infinitely distant from the tangent plane. |
| gnomonic  | TAN | A geometric projection of the celestial sphere onto a tangent plane from a centre of projection at the centre of the sphere. Displays all great circles  on the sphere as straight lines in the projection, resulting in any straight line segment showing the shortest route between the segment's two endpoints. |
| zenithal-equidistant  | ARC  | A useful application for this type of projection is a polar projection which shows all meridians (lines of longitude) as straight, with distances from the pole represented correctly.  |
| stereographic  | STG  | Projects a sphere onto a plane.  |
| plate-carree  | CAR  | The projection maps meridians to vertical straight lines of constant spacing (for meridional intervals of constant spacing), and circles of latitude to horizontal straight lines of constant spacing (for constant intervals of parallels).  |


### Performing a scan
Scans across a target: The scan starts at an offset, in degrees, from the target and ends at an offset, in degrees. These offsets are calculated in a projected coordinate system.
The only required input parameter is the _`target`_ as _`katpoint.Target`_ object, the following optional parameters are available:
* _`duration`_ : Minimum duration of each scan across target, in seconds
* _`start`_ : Initial scan position as (x, y) offset in degrees
* _`end `_ : Final scan position as (x, y) offset in degrees
* _`projection`_ : Name of projection in which to perform


### Performing a raster scan
A series of equidistant scans across a target performed by scanning in either azimuth or elevation.
The default is to `scan` in azimuth and `step` in elevation, leading to a series of horizontal scans.
The only required input parameter is the _`target`_ as _`katpoint.Target`_ object, the following optional parameters are available:
* _`num_scans`_ : Number of scans across target
* _`scan_duration`_ : Minimum duration of each scan across target, in seconds
* _`scan_extent`_ : Angular distance of scan along scanning coordinate, in degrees
* _`scan_spacing`_ : Separation between each consecutive scan in degrees
* _`scan_in_azimuth`_ : True if scanning in azimuth, while elevation remains fixed; False if scanning in elevation and stepping in azimuth instead
* _`projection`_ : Name of projection in which to perform
