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

- added the requirements to conda recipie'
needed to ajust some requirements because one was not yet in conda forge

# creating the conda build environemnt

```bash
conda create -n conda-build -c anaconda conda-build 
``` 

# Creating the meta.yaml file to create the conda package
see `meta.yaml`

- I need to get the package from PyPy and get the checksum
- then I need to put this checksum in the recipie

```bash
wget https://pypi.io/packages/source/g/genin2/genin2-2.1.2.tar.gz
sha256sum genin2-2.1.2.tar.gz
``` 

Error in build - we need to force conda to reload the packages
```bash
conda clean --packages
ls -la /cluster/projects/nn9305k/src/miniconda/pkgs/libgfortran-ng-15.1.0-h69a702a_2.conda
rm /cluster/projects/nn9305k/src/miniconda/pkgs/libgfortran-ng-15.1.0-h69a702a_2.conda
ls -la /cluster/projects/nn9305k/src/miniconda/pkgs/n* | grep "numpy-2.2.6" 
# rm /cluster/projects/nn9305k/src/miniconda/pkgs/numpy-2.2.6-py313h17eae1a_0.conda
```


# build the conda package
```bash
cd /cluster/projects/nn9305k/active/evezeyl/VIGAS-P/Galaxy_wrapper_genin2/genin2_conda_recipe
conda activate conda-build
conda build meta.yaml

# conda build meta.yaml  --config-file conda_build_config.yaml # it does not want to take numpy from this version
# conda build meta.yaml  --numpy 2.2.6
# It prefers a path and numpy needs to be specified manually otherwise it wont take it 
conda build .  --numpy 2.2.6
conda build purge # removes the attempt at build that did not work

```
# using the build to create the conda environment



copy the yml file
```bash
cd  /cluster/projects/nn9305k/src/miniconda/yaml_files
ls /cluster/projects/nn9305k/active/evezeyl/VIGAS-P/Galaxy_wrapper_genin2/genin2_conda_recipe/
cp /cluster/projects/nn9305k/active/evezeyl/VIGAS-P/Galaxy_wrapper_genin2/genin2_conda_recipe/genin2_2.1.2.yml  .

# If index is not found - but can try without (it seems it had been done)
GENIN_BUILD_PATH="/cluster/projects/nn9305k/active/evezeyl/VIGAS-P/Galaxy_wrapper_genin2/genin2_conda_recipe"
#"/cluster/projects/nn9305k/src/miniconda/envs/conda-build/conda-bld" -> where it did put the stuff for building
# cache for packages :  /cluster/projects/nn9305k/src/miniconda/pkgs/cache/
conda-build index $GENIN_BUILD_PATH

conda env create -f genin2_2.1.2.yml 
# conda env create -f genin2_2.1.2.yml -c $GENIN_BUILD_PATH - if need to specify the path

conda activate genin2_2.1.2
cd test
genin2 -o test.tsv EPI_ISL_18933204.fasta


conda deactivate
```


#tmux on login 4
# genin options to implement for VIGAS-O

available options
```bash
genin2 [OPTIONS] INPUT_FILE

  -h, --help                    Show this message and exit.
  -v, --version                 Show the version and exit.
  -o, --output-file FILENAME    Output TSV
  --loglevel [dbg|inf|wrn|err]  Verbosity of the logging messages  [default:
                                wrn]
  --min-seq-cov FLOAT RANGE     The minimum accepted sequence coverage for
                                each gene segment  [default: 0.7; 0<=x<=1]
```

- o --output-file
--min-seq-cov FLOAT RANGE