# TubeViT on the CWRU HPC (Pioneer Cluster)
I have found that 90% of doing research that involves computing is setting things up and troubleshooting and 10% is running the actual job.  This is an onofficial guide to how I run models on the HPC Pioneer cluster. abc124 is not anyone's actual ID number (as of 4/2025) \
Updated 4/24/2025

### Setup

get a caseID (abc124) 

get forticlient (the CWRU VPN) through the u[tech] site 

get an hpc account on pioneer and obtain keys 

upload and configure your keys through the terminal with the instructions from your PI 

log into forticlient and the hpc (abc124, SSO password), respond to duo push. If you're logging in from CaseWireless you can skip this step as you do not need the VPN.

create a gallina home (for Red Hen Users only) this is where you store large things (not your home directory). The exception to this is the model itself, more on that later. \
``` cd /path/to/gallina ``` \
``` mkdir abc124 ``` 

upload your model to your HOME directory ``` home/abc124 ``` 
and your dataset to wherever using globus.personal 

create a scratch directory. do not store things here, it will get deleted within 24h \
``` cd /scratch/users ``` \
``` mkdir abc124 ``` 

go back to your home directory \
``` cd /home/abc124 ``` 

create a virtual environment to run the model in \
``` module load Python/3.11.5 ``` \
``` python -m venv tubevit_venv ``` \
``` source tubevit_venv/bin/activate ``` \
``` pip install -U pip wheel ``` \
``` pip insteall -r requirements.txt ``` \

The model requires a lot of little dependencies that all have to be just right in order for it to run correctly.  The dependencies are things that the model expects to exist in the python environment and won't run without.  The virtual environment or ``venv`` is set up once so that the whole thing doesn't have to be configured each time. The downside is that ```venv``` cannot be moved once it's created, which is why the model and the ```venv``` are stored on the home directory. 

Jobs are run on compute nodes, which you get to from your home directory by requesting compute.  Usually, large things are stored on ```gallina``` and transferred to ```scratch``` in order to run the job. Virtual environments cannot be treated this way because once set up they can't be moved.  Get permission from your PI before setting one up on your home directory as they + the model can be large (>10gb) and each research group has a storage quota for home directories they have to stay under.

convert ViT weights to TubeViT \
``` python /home/abc124/TubeViT/scripts/convert_vit_weight.py ```

### Running a Training Cycle with UCF-101 \
(different steps are required to train tubevit on anything else as the model is currently configured for UCF-101. \ 
use ``` train --help ``` to figure out what you have to do to train on other datasets

Log into forticlient (abc124, SSO password)

load tmux \
``` tmux ```

Log into the hpc (abc124, SSO password)\
``` ssh abc124@pioneer.case.edu ```

go to gallina home to retrieve dataset \
``` cd /mnt/rds/redhen/gallina/home/abc124 ```

move dataset (UCF-101) from gallina to scratch (or Jester or EpicKitchens) \
``` mv UCF-101 /scratch/users/abc124 ```

check that dataset moved \
``` cd /scratch/users/abc124 ```

go to home \
``` cd /home/abc124 ```

request compute \
``` srun —mem=150gb --pty /bin/bash ```

load compatible python module \
``` module load Python/3.11.5-GCCcore-13.2.0 libffi/3.4.4-GCCcore-13.2.0 ```

activate virtual environment \
``` source tubevit_venv/bin/activate ```

train TubeViT on UCF-101 \
``` python /home/kxc750/TubeViT/scripts/train.py -r /path/to/dataset -a path/to/annotations``` 

