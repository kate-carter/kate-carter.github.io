# Initial Testing with ChatGPT for Video Classification of Co-Speech Gesture 
### 5/13/2025 
Today I started trials with ChatGPT for video classification using a tiered approach to assess it's capabilities. \
Some co-speech gestures are more "linguistic" than others in their technical qualifications. Most people know what a "shrug" or "pointing" but not the technical terms to describe "iconic, obj-proximal" or "iconic, fishing" or "one-hand beat".  Since ChatGPT has not been trained specifically for linguistic analysis, I'm not expecting it to hit all of the technical linguistic terms.  I am trying to evaluate what it's capacity for co-speech gesture classification and how granular/technical it can get. 

My first prompt was **"Can you tell me what co-speech gesture is performed in this video?"** along with an uploaded clip from the Ellen dataset of a man pointing. ```11-12-10_pointing.mp4``` \
11-12 is the clip number of the original dataset, 10 is the clip number out of the original video, and then "pointing" is the gesture label.  For a more detailed description of my dataset annotation, please see *6/10/2023, ELAN->ViViT Dataset Conversion*. \
The model returned a description of it's analysis, including the key frames it pulled from the video that it is analyzing, and then correctly identified it as "pointing".  It indicated that it would also be able to isolate the gesture with pose estimation, describe its alignment with speech context, and classify it into categories like "deictic, metaphoric, or beats" which are all supercategories my ViViT annotations hope to capture.  This indicates that video classification through ChatGPT may be able to output data in a format capturing more information than ViViT and TubeViT, which is desirable in replicating the depth of the ELAN annotations. 

My second prompt was **"Thank you! Let's try a harder one. This gesture inducates a location. Is the location proximal or distal?"** I thought this would be a good halfway point between asking it to identify a very basic gesture like pointing and asking it to identify more linguistically specific actions like beats and iconic gestures.  Having a prompt with specific words like "proximal" and "location" also help the model output salient results. \
It was able to extract frames and isolate the gesture, but ran out of alloted compute before it was able to complete the computation.  It informed me that I must wait 24 hours before performing a computationally expensive task again.  I'm currently finding out if my lab has a pro account I can use to continue this testing at a more reasonable timescale.

Since I already had a short referential conversation with the model about this, I asked it some questions about it's capabilities.  It responded that it could handle: 
1. **Frame Extraction and Preprocessing**, including arbitrary frame sampling intervals, cropping, and keyframe specification, as well as timestamps. 
2. **Pose Estimation** using MediaPipe or Openpose to detect 2D body landmarks to compute gesture, pointing vector, distance from body center.
3. **Gesture Classification** By combining landmark trajectories with simple heuristics or pre-trained classifiers, it can label gestures as: Deictic (pointing, showing), Iconic (pantomime shape), Metaphoric (abstract “holding idea” shapes), Beat gestures (rhythmic hand movements aligned with speech). It can further categorize “pointing” into proximal vs. distal by measuring the relative reach of the hand keypoint versus the torso or the camera.
4. **Audio/Text alignment** with provided audio, and can provide commentary and timestamps on gesture timing
5. **Visualization and Reporting** of hand-trajectory over time, overlay skeletons (like OpenPose), and present data tables. With ```python_user_visible```, it can also show DataFrames, matplotlib, or export files (like csv)

The model can't do livestreams, has compute limits, no internet-called API's or custom model training, but the out-of-the-box capabilities are massive.  

My final question for the  model was **"Can I upload more than one video and have you output classification labels per video?"** \
It responded that it could process each video individually, return a table or list with video filename, co-speech gesture type, sub-labels like proximal/distal, and timestamp in the clip that the gesture occurs.  This timestamping feature may be incredibly useful in pre-processing datasets for analysis by other machine vision systems like ViViT and TubeViT, which require one gesture per clip.  While the model has not indicated that it could pick out multiple gestures in one clip, it is able to take one video, isolate the timestamps of the gestures, and return clips with one gesture per clip.  It would then only be one additional step to prompt it to perform that chunking and then classify the videos.
The example output that it gave was: \
**Video**  **Gesture Type**  **Subtype**  **Time(s)** \
video1.mp4  Pointing           Distal      1.5-2.2 \
video2.mp4  Showing             --         1.0-3.7 \
video3.mp4  Pointing          Proximal     2.0-2.8 \
