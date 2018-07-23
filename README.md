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
  __eta__ [default=0.3, alias: learning_rate] 
  
  This is the shrinkage parameter &nu; as in GBM (see ESLII, 10.12.1 Shrinkage);
  
  `It is a regularization strategy, the smaller the value, the stronger (more conservative or robust) the regularization is.`
  
  range: [0, 1]
  
  typical range: [0.01, 0.2] as in [here](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/) 
  
  textbook suggestion: &nu; < 0.1 (see ESLII, 10.12.1 Shrinkage);
    
  __gamma__ [default=0, alias: min_split_loss]
  
  Minimum loss reduction required to make a further partition on a leaf node of the tree. 
    
  A node is split only when the resulting split gives a positive reduction in the loss function. Gamma specifies the minimum loss reduction required to make a split.

  Makes the algorithm conservative. The values can vary depending on the loss function and should be tuned.
  
  `The larger gamma is, the more conservative the algorithm will be.`
  
  range: [0,∞]
  
  __max_depth__ [default=6]
  
  Maximum depth of a tree. Increasing this value will make the model more complex and more likely to overfit. 0 indicates no limit. Note that limit is required when grow_policy is set of depthwise.
  
  This is the same as the `size of the tree` parameters *__J__* in GBM (see ESLII, 10.11 Right-Sized Trees for Boosting).
  
  range: [0,∞]

  textbook suggestion: [4, 8]
  
  typical range: [3, 10] as in [here](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/) 

  Although in many applications J = 2 will be insufficient, it is unlikely that J > 10 will be required. 
  
  Experience so far indicates that `4 < J < 8` works well in the context of boosting, with results being fairly insensitive to particular choices in this range. 

  One can fine-tune the value for J by trying several different values and choosing the one that produces the lowest risk on a validation sample. However, this seldom provides significant improvement over using J = 6.
  
  __min_child_weight__ [default=1]
  
  Minimum sum of instance weight (hessian) needed in a child. If the tree partition step results in a leaf node with the sum of instance weight less than min_child_weight, then the building process will give up further partitioning. In linear regression task, this simply corresponds to minimum number of instances needed to be in each node. The larger min_child_weight is, the more conservative the algorithm will be.

  `Hessian is the H term in objective function of XGBoost, if H is too small,  the obj will be `

  range: [0,∞]
  
  Defines the minimum sum of weights of all observations required in a child.

  This is similar to min_child_leaf in GBM but not exactly. This refers to min “sum of weights” of observations while GBM has min “number of observations”.
  
  Used to control over-fitting. Higher values prevent a model from learning relations which might be highly specific to the particular sample selected for a tree.

  Too high values can lead to under-fitting hence, it should be tuned using CV.
  
  
  
