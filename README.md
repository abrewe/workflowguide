How to run (BraggNNViewer, adSimServer, edgeBragg)
-
to use python in my simEmv conda environment 
/home/beams/ABREWE/miniconda3/envs/simEnv/bin/python

- BraggNNViewer  
  - Drops frames from edgebragg pretty badly... not sure why yet
  - Montage Sizing: "user set", with Montage Dim field set to n, makes an n x n montage.
  - Dynamic Sizing option does not work - ignore for now.
  - log option occasionally results in messed up contrast. 
  - Reset min and max peak intensity after using log (new max values might be too small)
  - Can put a usercalc pv name into PV Name: sends % of the patches from one frame that were 
  - located in ABREWE home directory ImageJ, run ImageJ there to access plugin (in folder EPICS_areaDetector)  
 
```shell
     cd home/beams/ABREWE/ImageJProjects/ImageJ
     
     java -jar ij.jar 
```

- adSimServer.py
  - In /home/beams/ABREWE/usr/pvaPy/pvapy/cli
  - modified to read in files (tiff, cbf tested, some GE files give incorrect size error) with fabio and streams frames to given channel name. 
     Ignores files it cannot read. Uses fabio. 
   - only loads files with single frames for now. 
  - adSimServer worked with EPICS_NTNDA_VIEWER up 500 fps with **randomly generated frames**, but does not seem to publish cbf files any faster than 18fps or so from /home/beams1/AMICELI/GMCA-Eiger-Pilatus-Data/eiger_M13/. Fps of adSimServer matches up until 18fps or so, any higher has problems. I'm thinking it's the larger image size, not sure . . . I can view/get higher frame rates (viewing with the NTNDA viewer) with smaller images.
  - adSimServer is set automatically right now to only try and load at most 100 frames at a time. I have loaded all files in /home/beams1/AMICELI/GMCA-Eiger-Pilatus-Data/eiger_M13/. It just takes a while. Number of files loaded can be changed for now with -nf.
```shell
cd /home/beams/ABREWE/usr/pvaPy/pvapy/cli

#reads in cbf files
/home/beams/ABREWE/miniconda3/envs/simEnv/bin/python ./adSimServer.py -id /home/beams1/AMICELI/GMCA-Eiger-Pilatus-Data/eiger_M13/ -cn abrewe:image:adsim -aff=1 -fps=10 -nf=50

#random
/home/beams/ABREWE/miniconda3/envs/simEnv/bin/python ./adSimServer.py -cn abrewe:image:adsim -fps=10

```

- edgebragg
  - I'm sure there is a way to run this without needing a conda environment but for now, you may need to create a clone of my simEnv conda env. There is a yml file that can be used to create a conda environment with all the packages I have in simEnv. (/home/beams/ABREWE/miniconda3/envs/environment_simEnv.yml) will create a conda environment called eb_adsim, change name in yml file to change the name. Then install pvapy after.
```sh
  conda env create -f /home/beams/ABREWE/miniconda3/envs/environment_simEnv.yml
  conda activate eb_adsim #or other name
  conda install -c sveseli pvapy
```

- need to set nproc to 1 in config yaml file for uniqueid to work with BraggNNViewer (/home/beams/ABREWE/usr/edgeBragg/config/sim.sf.yaml for example below - already set to 1)

```sh
#Ex: (specific to my home directory version):
  export PYTHONPATH=/home/beams/ABREWE/usr/edgeBragg/
  
  pvapy-hpc-consumer     --input-channel=abrewe:image:adsim   \
  --output-channel=abrewe:*:output     --control-channel=abrewe:*:control     \
  --status-channel=abrewe:*:status     \
  --processor-file=/home/beams/ABREWE/usr/edgeBragg/braggNNInferImageProcessor.py     \
  --processor-class=BraggNNInferImageProcessor     \
  --processor-args='{"configFile" : "/home/beams/ABREWE/usr/edgeBragg/config/sim.sf.yaml"}'     \
  --report-period=10     --log-level=DEBUG
```
```sh
#General ex:
$ export PYTHONPATH=/path/to/edgeBragg
$ pvapy-hpc-consumer \
    --input-channel=pvapy:image \
    --output-channel=bragg:*:output \
    --control-channel=bragg:*:control \
    --status-channel=bragg:*:status \
    --processor-file=/path/to/edgeBragg/braggNNInferImageProcessor.py \
    --processor-class=BraggNNInferImageProcessor \
    --processor-args='{"configFile" : "/path/to/edgeBragg/config/sim.sf.yaml"}' \
    --report-period=10 \
    --log-level=DEBUG
```
![example](https://user-images.githubusercontent.com/106117997/214919115-b9ec8672-4dbd-4caf-a7ad-deb88fd11923.png)

 
 

