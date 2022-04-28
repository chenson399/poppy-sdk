# Poppy Social Robotics SDK

___

#### Authors: Colin Henson, Sydney Awid, Hannah Stent

## Abstract
The Poppy Social Robotics Project utilizes the foundation of the [Poppy Project](https://poppy-project.org/en/) to construct a robot with social capabilities akin to basic human interaction. The end goal for the bot is to facilitate social interactions with children on the autism spectrum (ASD). The robot will perform tasks that replicate interpersonal communication using facial and speech recognition. The robot's head redesign houses the essential, sensory hardware and LCD. Therefore, the robot has a simple, friendly face that will emote on the LCD, and speak according to detected emotions using text-matching and randomized response. Additionally, the software will activate the servos posing as joints in the body and neck, to make social normative gestures with the face's response to user stimulus.

## Voice Recognition and Response
`` Insert just the way to call the entire class to make it work
Brief explanation on how it works and what it returns``

## poppy_face_tracking.py
Calling class properly:
```python
from poppy_face_tracking import poppy_tracking

face_tracking = poppy_tracking(cap1, model_path)
face_tracking.main_window()
```
This file will connect to a camera and the DYNAMIXEL servo motors for the Poppy robot to detect and track a face. The Poppy robot  was redesigned to use MX-28 for both neck servos, so check to make sure that the servo id’s for the servos you are using matches the id’s for your robot in the poppy_servo_control.py file. 
*** This file is meant to track one face only and if there are multiple faces detected, it can cause the servos to move erratically. 
camera: use 0 for default camera on laptop, 1 or other integer for external cameras

## poppy_face_track_with_eyes.py
Calling class properly:
```python
from poppy_face_tracking_with_eyes import poppy_tracking

face_tracking = poppy_tracking(cap1, image_path, model_path)
face_tracking.main_window()

```
This file will detect faces, track the biggest face with neck movements, and track the biggest face with eye movements. The eye movement is determined by the location of the target face compared to the reference rectangle that can be seen using the helpful_lines function. 

## poppy_emotion_recognition.py
`` Insert how to call the emotional recognition class
Brief explanation on how it works and what it returns``

## poppy_full_eye_tracking.py
```python
from poppy_full_eye_tracking import poppy_eye_tracker

eye_tracking = poppy_eye_tracker(cap1, cap2)
two_by_two_tracker = eye_tracking.two_by_two_eye_tracker()
```
This file is used to track eyes from the camera on Poppy and project where the subject is looking using a monitor looking at the robot. It contains 2x2, 3x3, and 4x4 eye tracking. It uses a modified gaze_tracking library to implement it. Cap1 is the camera on poppy, and cap2 is the camera looking at poppy. It creates two openCV windows to display the camera streams. Pressing the esc key will exit these windows.

## poppy_face_display.py
`` Insert how to call the facial emotion display
Brief explanation on how it works and what it returns``

## poppy_servo_control.py
`` Insert an example of a head and body gesture
Brief explanation on how it works and what it returns``

## poppy_speech_recognition.py
```python
from poppy_speech_recognition import poppy_speech

speech = poppy_speech(path_to_csv, audio_folder, faces_path, input_index)
run = speech.response_module()
```
This file is used for speech to text conversion, text matched responses, and human interaction. Path_to_csv, audio_folder_ and faces_path are the paths to 3 of the files in the Files folder. Input index is the device index of the microphone. Use 1 if using an onboard computer microphone. Use 2 if using the microphone attached to the robot. The code uses the microphone to convert human speech to text. This text is matched to the text in the phrases_document.csv file. Depending on the section the text is matched to, it will play a sound file, change its facial emotion, and do a gesture. 

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
