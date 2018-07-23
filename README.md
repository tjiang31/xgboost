# xgboost
  Primary source: https://xgboost.readthedocs.io/en/latest/parameter.html
  
## Before running XGBoost, we must set three types of parameters: general parameters, booster parameters and task parameters.

  __General parameters__ relate to which booster we are using to do boosting, commonly tree or linear model.
  `choosing booster`
  
  __Booster parameters__ depend on which booster you have chosen
  `most of the key parameters are tuned here`
  
  __Learning task parameters__ decide on the learning scenario. For example, regression tasks may use different parameters with ranking     tasks.
  `define the objective function and evaluation metrics, more computational optimization`
  
  __Command line parameters__ relate to behavior of CLI version of XGBoost.
  `Command line related, rarely used in my case`
  
  
# General Parameters
  __booster__ [default= gbtree ]
  
  Which booster to use. Can be _gbtree_, _gblinear_ or _dart_; 
  
  _gbtree_ and _dart_ use `tree based models` while _gblinear_ uses `linear functions`.
  
  _gbtree_ is most popular choice.
  
  [_dart_](https://xgboost.readthedocs.io/en/latest/tutorials/dart.html#dart-booster) is _gbtree_ with 'dropout'.
  

  __silent__ [default=0]
  
  0 means printing running messages, 1 means silent mode. `usually set to 1`
  
  
  __nthread__ [default to maximum number of threads available if not set]
  
  Number of parallel threads used to run XGBoost.
  
  __num_pbuffer__  `don't worry`
  
  __num_feature__ `don't worry`
  
  
# Parameters for Tree Booster
  *____ $\nu$
  
  
  
  
  
