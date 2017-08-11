# sweep-3d-scanner-unity-viewer
A simple unity project to view scans created by the [open source 3D scanner project](https://github.com/scanse/sweep-3d-scanner) in first person, including VR support.

## Using the Viewer

- Run the executable `Point_Cloud_Viewer.exe`.
- Once the app loads, a file browser should open automatically. 
- Select a CSV pointcloud file, with the expected format.
- Wait for the application to parse the file and render the points.
- Use `Q` & `E` to adjust the vertical position of the point cloud so the camera appears to be at head height.
- Alternatively, use `Z` & `C` to adjust the scale of the point cloud so it appears as a tiny model you can view like a diorama.
- To exit the application, simply `alt-tab` out and close the executable.

## Controls
- `WASD`: movement
- `Mouse`: look around
- `Q` & `E`: adjust the vertical position of the point cloud
- `Z` & `C`: adjust the scale of the point cloud

## Compatible file format
Only files with the expected format will open correctly. Attempting to open unexpected files will terminate the unity application. The application expects `.csv` files downloaded from the `sweep-3d-scanner` or exported from the `Sweep Visualizer` desktop application. 

If you want to convert custom data to make a compatible file, create a CSV file with the following format:
```csv
SCAN_INDEX,X,Y,Z,SIGNAL_STRENGTH
0,6.7,-124.2,-71.3,183
0,6.7,-125.4,-69.2,187
...
```

- SCAN_INDEX: is the index of the 2D slice of a 3D scan. This is a reference to the data acquisition process used by the 3D scanner, and is NOT used by the unity viewer. If you are making a file from scratch, simply use 0 for every entry.
- X,Y,Z: Integer or floating point values representing the x, y and z coordinates for each point. Units are in cm. Assumes a right handed coordinate system, where z is up. Note: this is NOT the same as unity's coordinate system, which is left handed with y up.
- SIGNAL_STRENGTH: integer value between [0:254] indicating strength/confidence in the sensor reading. Higher values are stronger readings, lower values are weaker readings. The color of points in the viewer are determined by this value, by mapping the range [0:254] to to entirety of the HUE spectrum in HSV color space. If you are generating a custom file, and do NOT have signal strength values for your data points, simply use the same value for every point. 


## Unity Project
### Packages/Dependencies
- `Standard Assets/Characters`: provides first person character controller
- `StandaloneFileBrowser`: enables using the native OS dialog to select a csv file, src available [here](https://github.com/gkngkc/UnityStandaloneFileBrowser).

### Included Assets
- `Scripts/CSVReader`: 
  - Naively parses a csv file of the expected structure into an array of `Vector4` objects. 
  - Each `Vector4` represents a point, where the first 3 elements (x,y,z) are position, and the 4th (w) is normalized signal strength.
- `Scripts/PointCloudGenerator`: 
  - Requests the user select a `.csv` file.
  - Reads the points from the file.
  - Since no single mesh can hold more than 65000 vertices, splits the points into multiple children each of which is a PointCloud object with a portion of the points. 
- `Scripts/PointCloud`: 
  - Creates a mesh where each vertex is a point, and the color for each vertex is calculated by the normalized signal strength.
- `Material/Shader_PointCloud`: Shader code, adapted from [here](http://www.kamend.com/2014/05/rendering-a-point-cloud-inside-unity/).

