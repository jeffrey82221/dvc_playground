# dvc_playground
Understand the usage of DVC and try to build an experiment platform from it

# Goal 

- [ ] Work though the tutorial
  - [X] Data Pipeline Versioning and Reproducing
  - [X] Experiment Tracking and Evaluation
  - [X] Experimenting using Pipelines [Reference](https://github.com/jeffrey82221/dvc_exp_playground) 
  - [ ] Model Registry
- [ ] Understand the usage of DVC python API package.


# Tutorial Notes:

## Step0: Setup tutorial code base

1. clone the code
```
wget https://code.dvc.org/get-started/code.zip
unzip code.zip && rm -f code.zip
```
2. setup virtual environment

```
virtualenv venv && echo "venv" > .gitignore
source venv/bin/activate
pip install -r src/requirements.txt
```
## Step1 (dvc): init dvc repo

```
dvc init
```

## Step2 (dvc): add input data to dvc

1. If no input data, pull data from data registry
```
dvc get https://github.com/iterative/dataset-registry \
          get-started/data.xml -o data/data.xml
```
2. add data to dvc
```
dvc add data/data.xml
```
Note: make sure data/data.xml is in .gitignore

## Step3 (dvc): Add stage to dvc

1. Add the three stages to dvc
```
dvc stage add -n prepare \
                -p prepare.seed,prepare.split \
                -d src/prepare.py -d data/data.xml \
                -o data/prepared \
                python src/prepare.py data/data.xml

dvc stage add -n featurize \
                -p featurize.max_features,featurize.ngrams \
                -d src/featurization.py -d data/prepared \
                -o data/features \
                python src/featurization.py data/prepared data/features

dvc stage add -n train \
                -p train.seed,train.n_est,train.min_split \
                -d src/train.py -d data/features \
                -o model.pkl \
                python src/train.py data/features model.pkl
dvc stage add -n evaluate \
  -d src/evaluate.py -d model.pkl -d data/features \
  -o eval \
  python src/evaluate.py model.pkl data/features
```
2. git commit after the stages are added.

```
git add dvc.yaml data/.gitignore
dvc config core.autostage true
```
## Step4 (dvc): Reproduce the stages 

1. using dvc to reproduce the pipeline taking the params.yaml as input
```
dvc repro
```
2. commit the lock file to git
```
git add dvc.lock && git commit -m "first pipeline repro"
```

## Step5 (dvc): Visualizing DVC pipeline DAG

```
dvc dag
```

## Step6 (dvc): Visualizing DVC evaluation scores

1. Showing the metrics of current reproduced experiment 

```bash
dvc metrics show
```

2. Showing the metrics difference between the current and previous experiment
```bash
dvc metrics diff
```

3. Visualizing the evaluation plots 

```bash
dvc plots show
```
* NOTE: This will generate a dvc_plots/index.html 

4. Comparing the evaluation plots for both the current and previous experiment

```bash
dvc plots diff
```

* NOTE: This will generate a dvc_plots/index.html with diagrams from both experiments

5. Comparing performance on all experiments

```
dvc exp show
```
## Step7 (dvc): Hyperparameter tuning:

```
dvc exp run --name "feature_tune" --queue \
-S 'featurize.max_features=20,40,60,80'
dvc queue start -j 1 -vv
```
# Reference:

https://dvc.org/doc/install/macos
