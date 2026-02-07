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

### Logic
1. **Tiling:** High-resolution images are split into overlapping tiles.  
2. **Tile-level detection:** Each tile is processed using a trained **YOLOv8** model specifically trained to detect screws.  
3. **Coordinate remapping:** Detections on tiles are mapped back to the original image coordinates.  
4. **Duplicate removal:** Non-Maximum Suppression (NMS) is applied to remove duplicates caused by overlapping tiles.  
5. **Visualization:** Final bounding boxes are drawn on the original image, and the screw count is printed.


### Libraries Used
- ultralytics – YOLOv8 object detection  
- opencv-python – Image processing and visualization  
- numpy – Array operations and calculations  


### How to Run

1. Install dependencies
````
pip install ultralytics opencv-python numpy
````
2. Update the img path to your image
3. Run the notebook cells

---

One of the key decisions during the project was in Task 1, where I had to choose between using the variance or the absolute sum of the Laplacian to measure image sharpness. It was settled to use variance, because in a blurred image, small fluctuations in intensity can accumulate and produce a high absolute sum that may be misleading, while the variance reliably detects sudden intensity changes associated with sharp edges.


## Task 3 - Robust Document OCR Preprocessing

Since working with ocr on low quality image is a hard challenge we did some modifications that gave almost a 80% improvment in the score of the ocr model after the modifications done on the image

The steps done are:

Deskewing which is automatically rotating an image to straighten tilted text so that it is horizontally aligned to improve OCR accuracy

Enhance contrast which depends on histogram equilization since images had issues in illumination (this step gave noticable improvments)

Binarization with adabtive gaussian thresholding because it handles uneven lighting and shadows and so it will work better for real-world ID photos where brightness varies across the card.

denoising by applying a blurring filter

We used random folders from the dataset and computed the baseline accuracy from the ocr model before modification and then after modification which gave a huge improvment

### Logic

1. Load Image: OpenCV reads the ID card image (TIF/JPG) from the MIDV-500 dataset.
2. Deskewing: The image is automatically rotated to align tilted text horizontally, improving OCR accuracy.
3. Contrast Enhancement: CLAHE (Contrast Limited Adaptive Histogram Equalization) enhances text visibility in dim or shadowed regions.
4. Binarization: Otsu thresholding converts the enhanced grayscale image into a clear black-and-white image for OCR.
5. Noise Removal: Median blur and morphological operations remove small artifacts and background noise.
6. OCR Comparison: Tesseract extracts text from both original and preprocessed images, and Levenshtein similarity is computed against ground truth JSON for evaluation.
7. Visualization: Original vs preprocessed images are displayed side-by-side, and optional bar charts/histograms show improvement in OCR similarity.


### Libraries Used

- OpenCV (cv2): Image reading, deskewing, contrast enhancement, thresholding, and noise removal.
- pytesseract: OCR engine to extract text from images.
- numpy: Array manipulation for image processing.
- matplotlib: Visualization of images and plotting of similarity scores.
- Levenshtein: Compute similarity of OCR output against ground truth text.
- os / json: File handling and loading MIDV-500 ground truth annotations.

### How to Run
1. Install dependencies:
````
pip install opencv-python pytesseract matplotlib python-Levenshtein
````
2. Update the BASE_PATH variable in the script to point to your MIDV-500 dataset root.
3. Run the script/notebook to process images, perform OCR, and calculate similarity scores.
4. Original and preprocessed OCR outputs, similarity scores, and images will be displayed automatically.
