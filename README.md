# Poppy Social Robotics SDK

___

#### Authors: Colin Henson, Sydney Awid, Hannah Stent

## Project Overview
The Poppy Social Robotics Project utilizes the foundation of the [Poppy Project](https://poppy-project.org/en/). Several changes have been made to the robot for our purposes. The AT-12 servos in the neck were replaced with MX28s to keep them consistent with the other servos and for the increased torque load. The head was redesigned using solidWorks and was 3D printed. This new head houses a LCD display, a speaker, a microphone, and a camera. This allows the robot to interact with the world around it. A new base was added for stability with a platform to house a Jetson Nano 4GB model. This can be used in the future to make the robot a self contained product. This code can be used with other poppy robots but the servo controls will need to be changed to work on a new poppy model. This project will work for Python 3.x.


## poppy_face_tracking.py
Calling class properly:
```python
from poppy_face_tracking import poppy_tracking

face_tracking = poppy_tracking(cap1, model_path)
face_tracking.main_window()
```
This file will connect to a camera and the DYNAMIXEL servo motors for the Poppy robot to detect and track a face. The Poppy robot  was redesigned to use MX-28 for both neck servos, so check to make sure that the servo id’s for the servos you are using matches the id’s for your robot in the poppy_servo_control.py file. 
 ### This file is meant to track one face only and if there are multiple faces detected, it can cause the servos to move erratically. 
camera: use 0 for default camera on laptop, 1 or other integer for external cameras

## poppy_face_track_with_eyes.py
```python
from poppy_face_tracking_with_eyes import poppy_tracking

face_tracking_with_eyes = poppy_tracking(cap1, image_path, model_path)
face_tracking_with_eyes.main_window()

```
This file will detect faces, track the biggest face with neck movements, and track the biggest face with eye movements. The eye movement is determined by the location of the target face compared to the reference rectangle that can be seen using the helpful_lines function. The neck movements are also determined by the target face location but it is compared to the mid-points of the input frame. The default resolution of the captured frame is 720 by 480, the resolution can be increased using opencv function setWindowProperty.This file will also open two windows, first window shows the captured frame and second will show the face of the robot. The faces are animated to simulate blinking and use .png files from the face_attributes file. Use the helpfule_lines function to see the midpoint reference, crosshair on the target, and reference rectangle for the eye movement. 

## poppy_emotion_recognition.py
```python
from poppy_emotion_recognition import poppy_emotion_recognizer

emotion_recognition= poppy_tracking(cap1, image_path, model_path)
emotion_recognition.main_window()

```
This file uses deepface library to do emotion analysis on a detected face and shows detected emotion on the screen. The code uses a try and except statement so that code does not stop when a face is not detected. It will return the dominant emotion and print the emotion on the video stream window that is detected from the passed frame.

## poppy_full_eye_tracking.py
```python
from poppy_full_eye_tracking import poppy_eye_tracker

eye_tracking = poppy_eye_tracker(cap1, cap2)
two_by_two_tracker = eye_tracking.two_by_two_eye_tracker()
```
This file is used to track eyes from the camera on Poppy and project where the subject is looking using a monitor looking at the robot. It contains 2x2, 3x3, and 4x4 eye tracking. It uses a modified gaze_tracking library to implement it. Cap1 is the camera on poppy, and cap2 is the camera looking at poppy. It creates two openCV windows to display the camera streams. Pressing the esc key will exit these windows.

## poppy_face_display.py
```python
from poppy_face_display import poppy_face

poppy_face = poppy_face(file_path)
poppy_face.setup_image(face_emotion)
```
This file uses the face_attributes folder to display pre-drawned facial images using opencv library. Use the correct file_path format based on your os. The face_emotion that is passed on the setup_image function is the file name of the image you want to display. Ex: To display 'angry.png', face_emotion = 'angry'. The code will format all images in the folder as long as it is a .png file and are stored in a dictionary which uses the file names as the keys.This code can also be used to display any .png file on the screen. 
## poppy_servo_control.py
```python
from poppy_servo_control import poppy_body_gesture

poppy_body_gesture = poppy_body_gesture()
```
This file uses the pypot library to connect and control the Dynamixel servo network to display body gestures. Body gestures were pre made and can be called through different functions such as set_to_neutral. This code also has some built in functions to help generate new body gestures. It is highly recommended to use the Dynamixel wizard to set the robot to a pose then use the pose_generator function to print code for the new pose. 
## poppy_speech_recognition.py
```python
from poppy_speech_recognition import poppy_speech

speech = poppy_speech(path_to_csv, audio_folder, faces_path, input_index)
run = speech.response_module()
```
This file is used for speech to text conversion, text matched responses, and human interaction. Path_to_csv, audio_folder and faces_path are the paths to 3 of the files in the Files folder. Input index is the device index of the microphone. Use 1 if using an onboard computer microphone. Use 2 if using the microphone attached to the robot. The code uses the microphone to convert human speech to text. This text is matched to the text in the phrases_document.csv file. Depending on the section the text is matched to, it will play a sound file, change its facial emotion, and do a gesture. 

## main_synced.py
```python
body_gesture.set_to_neutral()

face_tracking_process = threading.Thread(target=face_tracking.main_window)
emotion_detection_process = threading.Thread(target=poppy_emotion_recognizer.main_window)
face_display_process = threading.Thread(target=face_display.face_display)

face_tracking_process.start()
emotion_detection_process.start()
face_display_process.start()

time.sleep(15)


while run:
   target_window = win32gui.FindWindow(None, 'Poppy Face')
   shell = win32com.client.Dispatch("WScript.Shell")
   shell.SendKeys('%')
   win32gui.SetForegroundWindow(target_window)

   run = speech.response_module()
```
This file combines most of the subsystems covered above into a functional system. The files that use opencv are threaded so that they can run in the background without holding up the rest of the code. The sleep command allows the threads to load before continuing to the while loop. In the while loop, run defaults to true so that the while loop constantly runs. The middle code makes sure that the face display window is in focus so that the faces change as they should. The run variable calls the response module and returns True or False depending on what is said to the robot. If a farewell speech is detected, run is changed to False and the while loop is exited. The threads are stopped when the loop is exited.
