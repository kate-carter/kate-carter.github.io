# Running TubeViT on the CWRU HPC (Pioneer Cluster)

### Configuration:

Log into forticlient (abc124, SSO password)

load tmux \
``` tmux ```

Log into the hpc (abc124, SSO password)\
``` ssh kxc750@pioneer.case.edu ```

go to gallina home to retrieve dataset \
``` cd /mnt/rds/redhen/gallina/home/kxc750 ```

move dataset (UCF-101) from gallina to scratch (or Jester or EpicKitchens) \
``` mv UCF-101 /scratch/users/kxc750 ```

check that dataset moved \
``` cd /scratch/users/kxc750 ```

go to home \
``` cd /home/kxc750 ```

request compute \
``` srun —mem=150gb --pty /bin/bash ```

load compatible python module \
``` module load Python/3.11.5-GCCcore-13.2.0 libffi/3.4.4-GCCcore-13.2.0 ```

~To create virtual environment: ~~~~~~ \
``` module load Python/3.11.5 ``` \
``` python -m venv tubevit_venv ``` \
``` source tubevit_venv/bin/activate ``` \
``` pip install -U pip wheel ``` \
``` pip insteall -r requirements.txt ``` \
~~ ~~~ ~~~ ~~~ ~~~ ~~~ ~~~ ~~~ ~~~ ~~~

activate virtual environment \
``` source tubevit_venv/bin/activate ```

convert ViT weights to TubeViT \
``` python /home/kxc750/TubeViT/scripts/convert_vit_weight.py ```

### Running a Training Cycle on UCF-101
- to run a training cycle on any other dataset, check github for requirements

train TubeViT on UCF-101 \
``` python /home/kxc750/TubeViT/scripts/train.py -r /path/to/dataset -a path/to/annotations``` \
