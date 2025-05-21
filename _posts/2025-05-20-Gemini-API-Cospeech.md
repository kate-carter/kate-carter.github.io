# Gemini API (Gemini 2.0-001 Multimodal) for Co-Speech Gesture Annotation
### 5/20/2025

Gemini 2.0-001 is a multimodal Google GenAI model that can be accessed for free (with no discernible processing limits encountered) from the Google AI Studio.
You can get to it by googling "Gemini API" and getting to the studio page.  It analyzes both the audio and video of the input you give it.  To use the API, you have to generate keys and then write a script
that feeds Gemini a prompt and the data you want analyzed.  I used the suggested API script given by Gemini as a template and threw it into Cursor to add functionality. 
I ended up with a script that could input a video of one actor performing one gesture, between 3 and 10 seconds long, and get a description of 
the actor's movement and a gesture classification with a short description.

## Results
### Manual Testing through the Google AI Studio (Gemini 2.0 Flash)
I tested 50 videos from the Ellen 30 Videos Dataset (credit Peter Uhrig and the Max Planck Institute for Psycholinguistics). Each video was 
pre-annotated in two ways: It was cut down from the average 2-3 minutes to 3-10s fragments that each contained one actor performing one gesture.  Second, 
they were re-labeled to reflect the original video, the sequence it was cut from, and the gesture performed within it based on Bressen and Kendon's conventions.
For example, the 2nd video cut from ```11-11.mp4``` is ```11-11-2_twohandbeat.mp4```. 

**Prompt:  "Using linguistic terminology, can you classify the co-speech gesture in this clip?"** \
The results were somewhat inconsistent.
Sometimes I'd get two sections, one detailing the movement, and the other detailing the relation the gesture had to the speech, and sometimes I'd get a hallucination or 
something un-useful like "the actor is waving his hands around".  The limit seemed to be about five videos. I could run analysis on 5 videos and then the session would crash, and so 
I'd start another session and annotate another 5 videos.  I fiddled around with my prompt and then repeated this cycle 10 times and attempted to condense down my results into an xcel spreadsheet.
It took around 5 hours and yielded useful but tedious and unpredictable results.  Gemini was **97% accurate at describing the movement, and 60% accurate at returning a "valid" linguistic analysis**
of the gesture.  I say valid and not correct as some of the output was different than my own annotations, but still valid under Bressen's conventions.

Beautiful data, but it took a long time to get and standardize.  Due to this, I went and wrote an API script in Python to do it for me. The code to that set of API scripts
set up to analyze co-speech gesture are here: https://github.com/kate-carter/Gemini_API_Co-Speech and can be applied to other .mp4 videos containing one actor and one gesture.
Testing on multi-gesture videos will come in a later post.

### API-Automated Testing (Gemini 2.0-001 Multimodal)
For ease of testing, I developed this script using the first five videos of the chopped 30_Videos Ellen dataset. \
**Prompt: "Using linguistic terminology, can you describe the actor's movement and then classify the co-speech gesture in this video?".** \
It prints out an entry per video that looks like this:

### Video: 11-11-1_shrug.mp4 (Duration: 2.8 seconds)
### Analysis:
#### Action Performed:
Here's a breakdown of the co-speech gesture in the clip:
**Action:** The person in the clip extends both of their arms outwards with their palms facing upwards. 

**Co-Speech Gesture Category:** \
2.Co-speech Gesture Category: The gesture can be classified as an illustrator, specifically a beat and deictic gesture. It seems to emphasize and amplify what the person is saying. 

**Raw Response for Reference:** \
Here's a breakdown of the co-speech gesture in the clip: 
1.  Action: The person in the clip extends both of their arms outwards with their palms facing upwards.
2.  Co-speech Gesture Category: The gesture can be classified as an illustrator, specifically a beat and deictic gesture. It seems to emphasize and amplify what the person is saying. \
Processing Time: \
Total time: 6.9 seconds

After I run a trial with all 101 videos, I will update this post with overall performance metrics such as total processing time, accuracy, etc.  So far the outputs have been 
in sets of 5 (based on the manual limits), and have been much  more consistent than the manual trials.  Interestingly, it REALLY likes to give 
explanations for it's categorization and refuses to give any output without explanation without compromising the prompt.  I view this as continuance in the LLM trend of 
being a bit verbose.  

For data visualization, category responses will be cut down to "supercategory" "category" "subcategory" as necessary. \
E.g: Supercategory "Illustrator" \
Category "Beat" \
Sub-category "Emphasis" \
Each of these sections of the annotation can be marked valid or invalid so that
visualizations can be created to assess the model's performance at various levels of categorization.  So far it over-categorizes things as "Illustrators" but is 
very good at telling *what* an iconic gesture is, if it successfully recognizes that an iconic gesture is happening.

## Steps to use my API Script to analyze co-speech gesture.
1) Go to my GitHub for the project, https://github.com/kate-carter/Gemini_API_Co-Speech \
2) Fork the whole thing to your own repository/download so you can adapt it to your own OS (Mine was tested and run through MacOS Command line) \
3) Make Gemini API Keys \
4) Read the ReadMe file and follow its instructions. This will mostly involve changing out "/file/path/to/video" and "/file/path/to/output" to the actual filepaths you want Gemini to pull videos from and write output.  You also have to set your API Key as an environment variable.  I highly recommend setting up a virtual environment ```.venv```
   to run it in because it will not work if you have multiple Python interpreters running or the requirements have conflicts in the python environment.

## Steps I used to make this API Script Gemini 2.0-001
1) Went to the Gemini API and **generate API keys**. One set of keys can be used for any model you select. \
2) **Did some prompt testing** to verify that the prompt included in your API script will return the type of data I was looking for.
 I think I went through 5-6 iterations of my prompt before landing on one I liked. \
"Can you classify the co-speech gesture in this video?" \
"Using linguistic terminology, can you classify the co-speech gesture in this video?" \
"Using linguistic terminology, can you describe the actor's movement and then classify the co-speech gesture in this video?" \
(final) "Using linguistic terminology, can you classify the co-speech gesture in this clip in two sections: 1) the action being performed, and 2) the co-speech gesture category the gesture belongs to?"
3) **Put together my API script.** Gemini speaks python, so if you know how to write Python, you're in luck. If not, Gemini can probably also help you write one.  I used Cursor, which is an AI-assisted code. \
   editor to make this script, writing the bare minimum and then asking it to fill in functionality like formatting the data into a .docx and adding some extra data captures to it like processing time. \
5) **De-bugged the script** so that it worked. Cursor is great at writing scripts, but terrible at keeping track of what packages are running in the environment, what dependencies
   those packages have, etc.  I wrote a ```requirements.txt``` document and a ReadMe so that I (and anyone replicating my code) could have an easier time setting it up.
6) **Uploaded my work to GitHub** including a ReadMe, requirements.txt, and a .gitignore if necessary. Cursor can help you write these if you don't know how... But
I had to check that it actually included everything. Cursor is still an AI agent and while it is very useful, it does hallucinate, lack memory of its past actions and edits, and leave things out occasionally.

