# TubeViT on the CWRU HPC (Pioneer Cluster)
I have found that 90% of doing research that involves computing is setting things up and troubleshooting and 10% is running the actual job.  This is an unofficial guide to how I run models on the HPC Pioneer cluster as a member of the Distributed Little Red Hen Lab (mbt8 group) **using MacOS command line.**\
Updated 4/24/2025

### Setup

Get a caseID (eg abc124) 

Get forticlient (the CWRU VPN) through the u[tech] site 

Get an hpc account on pioneer and obtain keys 

Upload and configure your keys through the terminal with the instructions from your PI 

Log into forticlient and the hpc (abc124, SSO password), respond to duo push. If you're logging in from CaseWireless you can skip this step as you do not need the VPN.

Create a gallina home directory (for Red Hen Users only) this is where you store large files (not your home directory). The exception to this is the model itself, more on that later. \
``` cd /path/to/gallina ``` \
``` mkdir abc124 ``` 

Upload your model to your HOME directory ``` home/abc124 ``` 
and your dataset to wherever using globus.personal 

Create a scratch directory. do not store things here, it will get deleted within 24h \
``` cd /scratch/users ``` \
``` mkdir abc124 ``` 

Go back to your home directory \
``` cd /home/abc124 ``` 

Create a virtual environment to run the model in \
``` module load Python/3.11.5 ``` \
``` python -m venv tubevit_venv ``` \
``` source tubevit_venv/bin/activate ``` \
``` pip install -U pip wheel ``` \
``` pip insteall -r requirements.txt ``` 

Convert ViT weights to TubeViT \
``` python /home/abc124/TubeViT/scripts/convert_vit_weight.py ```

Exit compute node \
```exit```

Log out/close connection to the hpc \
```logout```

**Notes:**
The model requires a lot of little dependencies that all have to be just right in order for it to run correctly.  The dependencies are things that the model expects to exist in the python environment and won't run without.  The **virtual environment** or ``venv`` is set up once so that the whole thing doesn't have to be configured each time and contains most of these dependencies. The downside is that ```venv``` cannot be moved once it's created, which is why the model and the ```venv``` are stored on the home directory. Other dependencies have to be loaded with the python module (like ```libffe``` ) I've determined these mostly by trial and error as things aren't always listed in the Read.me file on the GitHub Repository. Shoutout to the hpc U[tech] team for helping me determine which need to be loaded in the python module.

Jobs are run on compute nodes, which you get to from your home directory by requesting compute.  Usually, large things are stored on ```gallina``` and transferred to ```scratch``` in order to run the job. Virtual environments cannot be treated this way because once set up they can't be moved.  Compute nodes do not have access to anything stored on Gallina. Get permission from your PI before setting one up on your home directory as they + the model can be large (>10gb) and each research group has a storage quota for home directories they have to stay under.

```tmux``` is a command line tool that allows you to set up a virtual window from your command line (on your home computer) that can be detached from your real command line.  This is useful because hpc jobs can take a very long time and will shut off if your connection is lost (i.e. when your screen turns off).  A windows alternative is ```screen```. You can install either using homebrew (very simple instructions online) and then the ```tmux``` commands relevant to this project are: \
``` tmux ``` starts a new tmux session \
```ctrl b d``` detaches your terminal from the session (so it can run without your oversight) The session will stay live even if your computer goes to sleep or you close your local terminal window. \
```ctrl b % ``` splits screen vertically. I have not figured out how to unsplit without terminating the session. \
``` ctrl b -> ``` switches your cursor from one window to another, depending on arrow direction. \
``` ctrl b :kill-session ``` ends the tmux session. \
```tmux a ``` reconnects you to the current session if you've detached. 


### Running a Training Cycle with UCF-101 
(different steps are required to train tubevit on anything else as the model is currently configured for UCF-101. \ 
use ``` train --help ``` to figure out what you have to do to train on other datasets

Log into forticlient (abc124, SSO password)

Load tmux \
``` tmux ```

Log into the hpc (abc124, SSO password)\
``` ssh abc124@pioneer.case.edu ```

Go to gallina home to retrieve dataset \
``` cd /mnt/rds/redhen/gallina/home/abc124 ```

Move dataset (UCF-101) from gallina to scratch (or Jester or EpicKitchens) \
``` mv UCF-101 /scratch/users/abc124 ```

Check that dataset moved \
``` cd /scratch/users/abc124 ```

Go to home \
``` cd /home/abc124 ```

Request compute \
``` srun —mem=150gb --pty /bin/bash ```

Load compatible python module \
``` module load Python/3.11.5-GCCcore-13.2.0 libffi/3.4.4-GCCcore-13.2.0 ```

Activate virtual environment \
``` source tubevit_venv/bin/activate ```

Train TubeViT on UCF-101 \
``` python /home/abc124/TubeViT/scripts/train.py -r /path/to/dataset -a path/to/annotations``` 

