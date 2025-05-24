# dvc_playground
Understand the usage of DVC and try to build an experiment platform from it

# Goal 

- [ ] Work though the tutorial
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
## Step2 (dvc): init dvc repo

```
dvc init
```

## Step3 (dvc): add input data to dvc

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



# Reference:

https://dvc.org/doc/install/macos