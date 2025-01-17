# Semantic Matcher and Learner
## Introduction

The Semantic Macther uses ASTNN model to embed the methods and the Wrod2vec to embed each word token in a bug report and gets the global semantic of the bug report by Bi-GRU. To measure the semantic similarity of the method embedding and bug report embedding which are not in the same vector space, a mlp is used to bridge the semantic gap of them, then the cosine similarity is calculated to get the semantic matching score. Besides, to alleviate the
sparsity and ambiguity of information in short methods, a short method expansion algorithm is also used in the Semantic Matcher.

The Learner combines the semantic mathing score calcaulated by the Semantic Matcher and the other three kinds of scores(bug fixing recency score, bug fixing frequency score, revised collaborative filtering score) to select the possible buggy methods from repository and rank them by supocious score.


## Environment

- OS: Ubuntu 18.04.4 LST
- Language: python 3.6.9
- pytorch: 1.0.0
- javalang: 0.11.0

## Dataset
 You need download the method level bug localization dataset of open-source projects (AspectJ, SWT and Tomcat) from [dataset](https://jbox.sjtu.edu.cn/l/J5z6bj).
```
cd MatcherAndLearner/
mkdir dataset
cd dataset
unrar x dataset.rar
```

You need download the relations data of methods and commits from [here](https://jbox.sjtu.edu.cn/l/45eBpZ).
and put it into  ```./output/project/sim/``` or specify the dir yourself in [config.py](./config.py)


## Usage
```
usage: BugPecker: Locating Faulty Methods with Deep Learning on Revision Graphs
       [-h] --project {swt,tomcat,aspectj} [--prepare] [--train] [--test]
       [--evaluate] [--gpu GPU] [--tr TR]

optional arguments:
  -h, --help            show this help message and exit
  --project {swt,tomcat,aspectj}
                        specify the java project to locate
  --prepare             prepare the data for training
  --train               train the model
  --test                test the model
  --evaluate            evaluate the model on test set
  --gpu GPU             specify gpu device
  --tr TR               specify the size of training set

```

### prepare
Prepare the data for training and testing a model. 

You need to run RevisionAnalyzer web service (see [RevisionAnalyzer](https://github.com/RAddRiceee/BugPecker/tree/master/RevisionAnalyzer) for help) and specify the url of versionInfo service in [config.py](./config.py).
You need to specify the project data to prepare, take 'tomcat' as an example.
```
	run.py --project tomcat --prepare
```
After the program has been successfully completed, you can see the prepared data in ```./output/tomcat/data/```.

### train 

To train a model for specific project.

```
	run.py --project tomcat --train
```

### test
To get the test result of the trained model in test set for specific project.
```
	run.py --project tomcat --test
```
### evaluate
To evaluate the test result.The metrics used to measure the result are hit@k,MAP and MRR.
```
	run.py --project tomcat --evaluate
```
## Result
The results show that BugPecker could localize bugs at method level more precisely than the baselines ([DNNLoc-m](https://doi.org/10.1109/ICPC.2017.24) and [BLIA 1.5](https://doi.org/10.1016/j.infsof.2016.11.002)), and achieving a MAP of 0.263 and a MRR of 0.291.
