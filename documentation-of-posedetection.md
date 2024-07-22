
# Pose Detection using MediaPipe and OpenCV

This project implements a real-time pose detection system using MediaPipe and OpenCV. The `poseDetector` class detects human poses, landmarks, and calculates angles between joints. The main script captures video input, processes it to detect poses, and displays the output with detected landmarks and angles.

## Installation

Ensure you have the following libraries installed:
- OpenCV
- MediaPipe

You can install these libraries using pip:

```bash
pip install opencv-python mediapipe
```

## Class `poseDetector`

### Constructor

```python
def __init__(self, mode=False, smooth=True, detectionCon=0.5, trackCon=0.5):
```

- `mode`: Boolean. If `True`, the detection is performed on static images.
- `smooth`: Boolean. If `True`, smoothes the landmarks.
- `detectionCon`: Float. Minimum detection confidence threshold.
- `trackCon`: Float. Minimum tracking confidence threshold.

### Methods

#### `findPose`

```python
def findPose(self, img, draw=True):
```

- `img`: The input image.
- `draw`: Boolean. If `True`, draws the pose landmarks on the image.

Returns the image with pose landmarks drawn.

#### `findPosition`

```python
def findPosition(self, img, draw=True):
```

- `img`: The input image.
- `draw`: Boolean. If `True`, draws circles on the detected landmarks.

Returns a list of landmark positions.

#### `findAngle`

```python
def findAngle(self, img, p1, p2, p3, draw=True):
```

- `img`: The input image.
- `p1`, `p2`, `p3`: Landmark indices to calculate the angle between.
- `draw`: Boolean. If `True`, draws lines and circles representing the angle.

Returns the calculated angle.

## Main Script

### `main` Function

The `main` function captures video from the default camera, processes it using the `poseDetector` class, and displays the results in real-time.

```python
def main():
    cap = cv2.VideoCapture(0)
    pTime = 0
    detector = poseDetector()
    while True:
        success, img = cap.read()
        if not success:
            break
        img = detector.findPose(img)
        lmList = detector.findPosition(img, draw=False)
        if len(lmList) != 0:
            print(lmList[14])
            cv2.circle(img, (lmList[14][1], lmList[14][2]), 15, (0, 0, 255), cv2.FILLED)
        cTime = time.time()
        fps = 1 / (cTime - pTime)
        pTime = cTime
        cv2.putText(
            img, str(int(fps)), (70, 50), cv2.FONT_HERSHEY_PLAIN, 3, (255, 0, 0), 3
        )
        cv2.imshow("Image", img)
        if cv2.waitKey(1) & 0xFF == ord("q"):  # Press 'q' to exit
            break
    cap.release()
    cv2.destroyAllWindows()
```

## Usage

1. Run the script.
2. The webcam feed will open.
3. Pose landmarks and angles will be displayed in real-time.
4. Press `q` to exit the video feed.

## Example

```bash
python pose_detection.py
```

The script will start capturing video from your webcam and display the pose detection results.
