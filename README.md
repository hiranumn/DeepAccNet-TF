<img src="figures/ipdlogo.png">

# DeepAccNet.py
Implemenation of DeepAccNet described in https://www.biorxiv.org/content/10.1101/2020.07.17.209643v1

This method will estimate how good your protein models are using a metric called l-DDT (local distance difference test).

```
usage: DeepAccNet.py [-h] [--pdb] [--csv] [--ensemble] [--leaveTempFile] [--verbose] [--process PROCESS] [--gpu GPU]
                     [--featurize] [--reprocess]
                     input ...

Error predictor network

positional arguments:
  input                 path to input folder or input pdb file
  output                path to output (folder path, npz, or csv)

optional arguments:
  -h, --help            show this help message and exit
  --pdb, -pdb           Running on a single pdb file instead of a folder (Default: False)
  --csv, -csv           Writing results to a csv file (Default: False)
  --ensemble, -e        Running with ensembling of 4 models. This adds 4x computational time with some overheads
                        (Default: False)
  --leaveTempFile, -lt  Leaving temporary files (Default: False)
  --verbose, -v         Activating verbose flag (Default: False)
  --process PROCESS, -p PROCESS
                        Specifying # of cpus to use for featurization (Default: 1)
  --gpu GPU, -g GPU     Specifying gpu device to use (default gpu0)
  --featurize, -f       Running only the featurization part(Default: False)
  --reprocess, -r       Reprocessing all feature files (Default: False)

v0.0.1
```

# Required softwares
- Python3.5>
- Pyrosetta 
- Tensorflow 1.14 (not Tensorflow 2.0)
- Tested on Ubuntu 20.04 LTS

# Example usages (for IPD users)
Type the following commands to activate tensorflow environment with pyrosetta3.
```
source activate tensorflow
```

Running on a folder of pdbs (foldername: ```samples```)
```
python DeepAccNet.py -r -v samples outputs
```
Running on a folder of pdbs (foldername: ```samples```) and report to a csv file (outputname: ```output.csv```).
```
python DeepAccNet.py -r -v --csv samples output.csv
```

Running on a single pdb file (inputname: ```input.pdb```). Output name is optional and defaults to ```input.npz```.
```
python DeepAccNet.py -r -v --pdb input.pdb [output.npz]
```

Only doing the feature processing (foldername: ```samples```)
```
python DeepAccNet.py -r -v -f samples outputs
```

# How to look at outputs
Output of the network is written to ```[input_file_name].npz```, unless you had the ```--csv``` flag on.
You can extract the predictions as follows.

```
import numpy as np

x = np.load("testoutput.npz")

lddt = x["lddt"]           # per residue lddt
estogram = x["estogram"]   # per pairwise distance e-stogram
mask = x["mask"]           # mask predicting native < 15
```
Perhaps ```lddt``` is the easiest place to start as it is per-residue quality score. You can simply take an average if you want a global score per protein structure. 

If you want to do something more involved, especially for protein complex design, see [example.ipynb](ipynbs/example.ipynb) for getting more specialized metrics. If you want to play with pair-wise error predictions, [samples.ipynb](ipynbs/samples.ipynb) is a good place to start.

# Trouble shooting
- If DeepAccNet.py returns an OOM (out of memory) error, your protein is probably too big. Try getting on titan instead of rtx2080 or run without gpu if running time is not your problem. You can also run it on cpus although it would be slow.
- If you get an import error for pyErrorPred, you probably moved the script out of the DeepAccNet folder. In that case, you would have to add pyErrorPred to python path or do so within the script. 
- Send an e-mail at hiranumn at cs dot washington dot edu.

# Updates
- Repo initialized 2020.7.20
