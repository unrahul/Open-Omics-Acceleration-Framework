
# Download entire repository
```bash
git clone --recursive https://github.com/IntelLabs/Open-Omics-Acceleration-Framework.git
cd ~/Open-Omics-Acceleration-Framework/pipelines/single_cell_pipeline
```

# Instructions to Run
We can run this pipeline in three ways: 1. Docker container, 2. Using anaconda environment file, 3. Creating anaconda environment manually.   

## (Option 1): Docker instructions (Recommended on Cloud Instance)
```bash
# Default values of environment variables is set to NUMEXPR_MAX_THREADS=64, NUMBA_NUM_THREADS=64  (Number of CPUs)
# inside the docker image. Update Dockerfile to change these variables according to number of CPUs for best performance

cd ~/Open-Omics-Acceleration-Framework/pipelines/single_cell_pipeline/
docker build -t scanpy .           # Create a docker image named scanpy

# Download dataset
wget -P ~/Open-Omics-Acceleration-Framework/pipelines/single_cell_pipeline/data https://rapids-single-cell-examples.s3.us-east-2.amazonaws.com/1M_brain_cells_10X.sparse.h5ad

docker run -it -p 8888:8888 -v ~/Open-Omics-Acceleration-Framework/pipelines/single_cell_pipeline/data:/data scanpy   # run docker container with the data folder as volume
```

## (Option 2): Create an Anaconda environment from file
```bash
conda env create --name=single_cell -f environment.yml
conda activate single_cell
```

### Replace the _t_sne.py file to anaconda environment's daal4py package
```bash
cp _t_sne.py ~/anaconda3/envs/single_cell/lib/python3.8/site-packages/daal4py/sklearn/manifold/
```

### Install umap_extend and umap 
```bash

pip uninstall umap-learn
cd ~/Open-Omics-Acceleration-Framework/lib/tal/applications/UMAP_fast/umap_extend
python setup.py install                          # Uncomment AVX512 lines in setup.py before doing this step on avx512 machines


cd ~/Open-Omics-Acceleration-Framework/lib/tal/applications/UMAP_fast/umap
python setup.py install                     # do python setup.py install if moving environment using conda-pack
```


### Example Dataset
The dataset was made publicly available by 10X Genomics. Use the following command to download the count matrix for this dataset and store it in the data folder:
```bash
wget -P ~/Open-Omics-Acceleration-Framework/pipelines/single_cell_pipeline/data https://rapids-single-cell-examples.s3.us-east-2.amazonaws.com/1M_brain_cells_10X.sparse.h5ad
```

### Setup and run
```bash
export NUMEXPR_MAX_THREADS=56          # equal to number of threads on a single socket
export NUMBA_NUM_THREADS=56            # Remember to delete __pycache__ folder from local directory and umap/umap/ directory if increasing number of threads

# also update sc.settings.n_jobs=56 to set number of threads inside 1M_brain_cpu_analysis.py

cd ~/Open-Omics-Acceleration-Framework/pipelines/single_cell_pipeline/notebooks/

# Or the jupyter notebook with sklearn patch in it. 
# from sklearnex import patch_sklearn
# patch_sklearn()

jupyter notebook
```


## (Alternatively, Option - 3) You can also create Anaconda environment Manually
```bash
conda create --name single_cell python=3.8.0
conda activate single_cell
```

### Necessary scanpy tools
```bash
conda install -y seaborn=0.12.2 scikit-learn=1.0.2 statsmodels=0.13.2 numba=0.53.1 pytables=3.7.0 matplotlib-base=3.6.2 pandas=1.5.2
conda install -y -c conda-forge mkl-service=2.4.0
conda install -y -c conda-forge python-igraph=0.10.3 leidenalg=0.9.1
conda install -y -c conda-forge cython=0.29.33 jinja2=3.1.2 clang-tools=15.0.7
conda install -y -c katanagraph/label/dev -c conda-forge katana-python
```

### Install scanpy
```bash
pip install scanpy==1.8.1
```

### Install scikit-learn intel extension (PIP version)
```bash
pip install scikit-learn-intelex==2023.0.1
```
### Install other packages
```bash
pip install pybind11
pip install jupyterlab
pip install wget
```

### Replace the _t_sne.py file to anaconda environment's daal4py package
```bash
cp _t_sne.py ~/anaconda3/envs/single_cell/lib/python3.8/site-packages/daal4py/sklearn/manifold/
```

### Install umap_extend and umap 
```bash

pip uninstall umap-learn
cd ~/Open-Omics-Acceleration-Framework/lib/tal/applications/UMAP_fast/umap_extend
python setup.py install                          # Uncomment AVX512 lines in setup.py before doing this step on avx512 machines


cd ~/Open-Omics-Acceleration-Framework/lib/tal/applications/UMAP_fast/umap
python setup.py install                     # do python setup.py install if moving environment using conda-pack
```