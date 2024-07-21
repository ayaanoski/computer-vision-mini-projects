
# Real-Time Face Detection Using Mediapipe and OpenCV

## Overview
This script performs real-time face detection using the webcam feed. It utilizes the `mediapipe` library for face detection and `opencv` for video capture and display. The detected faces are highlighted with bounding boxes, and the detection confidence scores are displayed.

## Requirements
- Python 3.x
- OpenCV (`opencv-python`)
- Mediapipe

## Installation
Install the required libraries using pip:
```bash
pip install opencv-python mediapipe
```

## Code Explanation

### Importing Libraries
```python
import cv2
import mediapipe as mp
import time
```
- `cv2`: OpenCV library for video capture and processing.
- `mediapipe`: Library for face detection.
- `time`: Standard library for calculating FPS (frames per second).

### Initializing Webcam and Mediapipe Face Detection
```python
cap = cv2.VideoCapture(0)
pTime = 0

mpFaceDetection = mp.solutions.face_detection
mpDraw = mp.solutions.drawing_utils
faceDetection = mpFaceDetection.FaceDetection(0.75)
```
- `cap`: Captures video from the default webcam (`0` is the index for the default webcam).
- `pTime`: Stores the previous time for FPS calculation.
- `mpFaceDetection`: Mediapipe face detection module.
- `mpDraw`: Utility for drawing functions.
- `faceDetection`: Initializes the face detection with a confidence threshold of 0.75.

### Main Loop for Real-Time Face Detection
```python
while True:
    success, img = cap.read()
    if not success:
        break

    imgRGB = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
    results = faceDetection.process(imgRGB)
    # print(results)

    if results.detections:
        for id, detection in enumerate(results.detections):
            bboxC = detection.location_data.relative_bounding_box
            ih, iw, ic = img.shape
            bbox = (
                int(bboxC.xmin * iw),
                int(bboxC.ymin * ih),
                int(bboxC.width * iw),
                int(bboxC.height * ih),
            )
            cv2.rectangle(img, bbox, (255, 0, 255), 2)
            cv2.putText(
                img,
                f"{int(detection.score[0] * 100)}%",
                (bbox[0], bbox[1] - 20),
                cv2.FONT_HERSHEY_PLAIN,
                2,
                (255, 0, 255),
                2,
            )

    cTime = time.time()
    fps = 1 / (cTime - pTime)
    pTime = cTime
    cv2.putText(
        img, f"FPS: {int(fps)}", (20, 70), cv2.FONT_HERSHEY_PLAIN, 3, (0, 255, 0), 2
    )
    cv2.imshow("Image", img)
    if cv2.waitKey(1) & 0xFF == ord("q"):
        break
```
- **Video Capture**: Reads frames from the webcam in a loop.
  - `success, img = cap.read()`: Reads a frame from the webcam.
  - `if not success`: Breaks the loop if the frame is not captured successfully.
- **Face Detection**:
  - `imgRGB = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)`: Converts the frame from BGR to RGB color space.
  - `results = faceDetection.process(imgRGB)`: Processes the frame for face detection.
  - `if results.detections`: Checks if any faces are detected.
  - **Bounding Box and Score**:
    - Loops through detected faces and extracts bounding box coordinates.
    - `ih, iw, ic = img.shape`: Gets the image dimensions.
    - `bbox`: Calculates bounding box coordinates relative to the image size.
    - `cv2.rectangle(img, bbox, (255, 0, 255), 2)`: Draws a rectangle around the detected face.
    - `cv2.putText(img, f"{int(detection.score[0] * 100)}%", (bbox[0], bbox[1] - 20), cv2.FONT_HERSHEY_PLAIN, 2, (255, 0, 255), 2)`: Displays the detection confidence score.
- **FPS Calculation**:
  - `cTime = time.time()`: Gets the current time.
  - `fps = 1 / (cTime - pTime)`: Calculates FPS.
  - `pTime = cTime`: Updates the previous time.
  - `cv2.putText(img, f"FPS: {int(fps)}", (20, 70), cv2.FONT_HERSHEY_PLAIN, 3, (0, 255, 0), 2)`: Displays the FPS on the frame.
- **Display and Exit**:
  - `cv2.imshow("Image", img)`: Displays the frame.
  - `if cv2.waitKey(1) & 0xFF == ord("q")`: Exits the loop if the 'q' key is pressed.

### Releasing Resources
```python
cap.release()
cv2.destroyAllWindows()
```
- `cap.release()`: Releases the webcam resource.
- `cv2.destroyAllWindows()`: Closes all OpenCV windows.

## Usage
Run the script, and it will open a window displaying the webcam feed with detected faces highlighted. Press 'q' to exit the application.
