# prebuild to get the requirements and do that proprely
```bash
conda create -n genin2 python=3.13
conda activate genin2

pip install pipreqs
pip install genin2==2.1.2

genin2 --help 
cd  /cluster/projects/nn9305k/src/miniconda/envs/genin2
pipreqs .
pipreqs . --encoding=windows-1252 # nope still error
pipreqs . --encoding=latin-1 # nope still error
 pip uninstall pipreqs

# so trying with pip freeze
pip freeze > requirements.txt
conda deactivate
```

- added the requirements to conda recipie

# creating the conda build environemnt

```bash
conda create -n conda-build -c anaconda conda-build 
``` 

# Creating the meta.yaml file to create the conda package
see `meta.yanl`

# build the conda package
```bash
cd /cluster/projects/nn9305k/active/evezeyl/VIGAS-P/Galaxy_wrapper_genin2/genin2_conda_recipe
conda activate conda-build
conda build meta.yaml
conda deactivate
```

# using the build to create the conda environment
```bash
conda env create -f /cluster/projects/nn9305k/active/evezeyl/VIGAS-P/Galaxy_wrapper_genin2/genini2.yml
```

# genin options to implement

genin2 [OPTIONS] INPUT_FILE

  -h, --help                    Show this message and exit.
  -v, --version                 Show the version and exit.
  -o, --output-file FILENAME    Output TSV
  --loglevel [dbg|inf|wrn|err]  Verbosity of the logging messages  [default:
                                wrn]
  --min-seq-cov FLOAT RANGE     The minimum accepted sequence coverage for
                                each gene segment  [default: 0.7; 0<=x<=1]
