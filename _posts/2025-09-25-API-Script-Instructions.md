For those who would like to use my pipeline and do not have a programming background: 
I'm sorry that there is no GUI, front-end design has never been my strong suit.  What I can offer
is a quick-and-dirty step-by-step run down of the practical things you need to run my script.

Important: This can be run on any audiovisual dataset and does not need to be run specifically on gesture
or linguistic data at all, it's just a framework for data analysis using the Gemini API.
Last updated: 9/25/2025 for use with Gemini 2.0 and 2.5. It probably works for newer models, but I have not tested that personally 
(see "using the script" section below)

**Steps**

1. System Requirements: 
You need an IDE. This is essentially an environment that can run Python code. 
I recommend Pycharm (what this program was written in) or Cursor, which is essentially Pycharm with 
Claude built in. If you're worried about the programming aspect, I recommend Cursor because Claude will be able to 
access the script and answer questions about it in case you need a quick and detailed explanation of something. 
As a undergrad or grad student, Cursor should be free. If you are not either of these things I believe there is a subscription.
[add download links]

2. Setting up Gemini API:
This is a Gemini API script. Essentially what this means is this script organizes the data you're sending up, sends it to Gemini, 
and then pulls back down the result. As such, there are a few things you need to set up with the Google API.
2a. Get Gemini API keys. You may have to use your personal email for this as CWRU has disabled access for some account types. 
I'm currently an affiliate and not a student so I don't know what your access will be but you can email help@case.edu if you have issues. 
Go to Google API Studio and create an API key. Save this in a notes app or something. You need it for later.
2b. Save this link to check somewhere. It's a list of what Google models are available and how many model calls you get per day. 
You may have to spread out testing based on how many calls you have for a given model. You want to pay attention to "RPD" Requests per day. 
There's a variable set in my api script that you can set to make sure you don't go over and get charged. I typically set it for 10-20 calls 
under threshold to be safe.

3. Setting up the program in your IDE. This step will vary based on which IDE you're using.  There are a lot of tutorials online.  
I'd recommend creating a Github account if you don't have one already so you can fork (create your own copy) of my code in order to make edits if 
you need to. Github can be kind of hard to navigate if you haven't used it before so if it gets complicated, just fork it to your account and copy paste 
it into your IDE. If you want Github to auto-sync with what's saved on your laptop, I can find a tutorial for you on youtube.  
Here's my github repository for the project. It's important to load all of the files into your IDE, not just the API script you want, 
as you have to check the requirements.txt and the gitignore in order for the rest of it to run properly.  
This is a good thing to ask ChatGPT or Claude if you're stuck.
3a. Download the repo to your computer and then open it in Pycharm or Cursor as a new project.  
Follow the README instructions.  

4. Using the API Script. There are six important parts of the script: 
1) setting your API Key.  The program will prompt you to do this automatically at the beginning of each session. 
2) specifying what model you want. 
3) picking where the word document the script creates will save. 
4) picking the file path to the context document if you have a context document.  
If you want to use context just pick an api script labeled "context" and if you don't want context pick one labeled "nocontext".

5) writing your prompt and putting it in the "ANALYSIS_PROMPT" variable.
[picture]
6) setting the files to analyze. I usually keep these all in the same folder and then 
copy paste the absolute file paths and just change the file name.
[picture]
Bonus: Here's where the call limit variable can be set:
[picture]

If you're having a hard time finding any of these sections, I've included the line numbers in these screenshots.  
If you have a hard time finding the absolute file paths for a file, windows computers usually have it written at 
the top of your "files" window and macs have a shortcut in finder that's googleable.  It's very important to use the absolute file paths.  
There's pretty good error handling so if you fudge it the program should tell you what's wrong and then you can 
stack overflow or ChatGPT how to fix it.  
