# dark_pyramids_intenship
This repository solves the dark pyramids internship assignment for computer vision

## Task 1 - The "Sharp-Shot" Video Filter
As we know sharp images means image that contains sharp edges
Edges are the regions where intensity changes

Calculating the first derivative shows the rate of change of intensity so in blurred images these changes are spread over a wider area, resulting in weaker and less localized edge responses

Calculating second derivative shows the rate of change of the rate of change and this means that for sharp edges (sudden intensity changes) there will be large values.

Second derivative can be calculated with Laplacian function

Then we can calculate variance of the output for all images to check which images had the highest variance meaning the highest sudden jumps in intensity (sharpest edges) and highest spread of values and those will be considered the sharpest images

### Logic
1. **Load Video:** OpenCV reads the video file frame by frame.  
2. **Grayscale Conversion:** Each frame is converted to grayscale to focus on intensity changes rather than color.  
3. **Compute Sharpness:** The Laplacian operator computes the second derivative of intensity. The variance of the Laplacian is used as a sharpness metric.  
4. **Select Top Frames:** Maintain the top 5 frames with the highest Laplacian variance. A heap can be used for efficiency in long videos.  
5. **Ensure Diversity:** To avoid selecting frames from the same second, track the video timestamp and skip frames from already selected seconds.  
6. **Save & Visualize:** The selected frames are saved as images and plotted side-by-side for qualitative verification.


### Libraries Used
- **OpenCV (cv2):** Video reading, frame processing, Laplacian computation.  
- **heapq:** Efficiently maintain top K sharpest frames.  
- **matplotlib:** Plotting frames for visualization.  
- **os:** File handling and directory management.


### How to Run
1. Install dependencies:
````
pip install opencv-python matplotlib
````
3. Update the video_path variable in the script to point to your video file.
4. Run the notebook cells in order
4. The top 5 frames will be saved to the top_frames folder and plotted automatically.

---

## Task 2 - Small Object Detection Strategy

Since tiny objects are lost when we use models like YOLO

We will use slicing approach

We will devide the 4k image into smaller size images (ex:640x640)

Then we overlap those images by some percentage (each box represent one of the smaller images)

This overlapping regions will help in case we had some screws that was split at the edge so it will prevent them from not being taken into consideration

What about if screws are counted more than once (they appear fully in the intersecting region of 2 images so they are counted twice)? We will store the coodrinates of all screws detected so if it is detected twice we can show it only once

Regarding running a model on a Raspberry-pi using such values will be take so much computational power so we can:

Make the tile image dimentions larger so few tiles
Reducing overlap percentage

# Screw Detection with YOLOv8 and Tiling (SAHI-style)


### Logic
1. **Tiling:** High-resolution images are split into overlapping tiles.  
2. **Tile-level detection:** Each tile is processed using a trained **YOLOv8** model specifically trained to detect screws.  
3. **Coordinate remapping:** Detections on tiles are mapped back to the original image coordinates.  
4. **Duplicate removal:** Non-Maximum Suppression (NMS) is applied to remove duplicates caused by overlapping tiles.  
5. **Visualization:** Final bounding boxes are drawn on the original image, and the screw count is printed.


## Libraries Used
- ultralytics – YOLOv8 object detection  
- opencv-python – Image processing and visualization  
- numpy – Array operations and calculations  


## How to Run

1. Install dependencies
````
pip install ultralytics opencv-python numpy
````
2. Update the img path to your image
3. Run the notebook cells

---

One of the key decisions during the project was in Task 1, where I had to choose between using the variance or the absolute sum of the Laplacian to measure image sharpness. It was settled to use variance, because in a blurred image, small fluctuations in intensity can accumulate and produce a high absolute sum that may be misleading, while the variance reliably detects sudden intensity changes associated with sharp edges.
