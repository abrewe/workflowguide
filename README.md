How to run (edgeBragg, adSimServer BraggNNViewer)
-
to use python in my conda environment 
/home/beams/ABREWE/miniconda3/envs/simEnv/bin/python

- BraggNNViewer  
  - log option sometimes results in messed up contrast. 
  - Reset min and max peak intensity after using log (new max values might be too small)
  - Can put a usercalc pv name into PV Name: sends % of the patches from one frame that were 
  - located in ABREWE home directory ImageJ, run ImageJ there to access plugin (in folder EPICS_areaDetector) 
  - Montage Sizing: "user set", with Montage Dim field set to n, makes an n x n montage. 
```shell
     cd home/beams/ABREWE/ImageJProjects/ImageJ
     
     java -jar ij.jar 
```

- adSimServer.py
  - In /home/beams/ABREWE/usr/pvaPy/pvapy/cli
  - modified to read in files (tiff, cbf tested, some GE files give incorrect size error) with fabio and streams frames to given channel name. 
     Ignores files it cannot read. Uses fabio. 
  - adSimServer reportedly keeps up with fps well (tried up to 100fps), but EPICS_NTNDA_Viewer does not seem to get them 
  faster than 18fps or so. Fps of adSimServer matches up until 18 or so, any higher has problems. Not sure which end the problem's on. 
```shell
cd /home/beams/ABREWE/usr/pvaPy/pvapy/cli

/home/beams/ABREWE/miniconda3/envs/simEnv/bin/python ./adSimServer.py -id /home/beams1/AMICELI/GMCA-Eiger-Pilatus-Data/eiger_M13/ -cn abrewe:image:adsim -aff=1
```

- edgebragg
  - might need a conda environment for this to work properly - if so see below 
  - need to set nproc to 1 for uniqueid to work with BraggNNViewer
```sh
Ex (specific to my home directory version):
  export PYTHONPATH=/home/beams/ABREWE/usr/edgeBragg/
  
  pvapy-hpc-consumer     --input-channel=abrewe:image:adsim   
  --output-channel=abrewe:*:output     --control-channel=abrewe:*:control     
  --status-channel=abrewe:*:status     
  --processor-file=/home/beams/ABREWE/usr/edgeBragg/braggNNInferImageProcessor.py     
  --processor-class=BraggNNInferImageProcessor     
  --processor-args='{"configFile" : "/home/beams/ABREWE/usr/edgeBragg/config/sim.sf.yaml"}'     
  --report-period=10     --log-level=DEBUG
```
General ex:
```sh
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

<ins>CONDA ENVIRONMENT</ins> (just a list of what I have in my simEnv conda environment)

Pytorch  
Pyyaml  
Pvapy   
Numpy   
Fabio   
OpenCV/cv2
PIL/pillow   
H5py   
  

