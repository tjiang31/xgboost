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
  
  &gamma; Minimum loss reduction required to make a further partition on a leaf node of the tree. 
    
  A node is split only when the resulting split gives a positive reduction in the loss function. Gamma specifies the minimum loss reduction required to make a split.

  Makes the algorithm conservative. The values can vary depending on the loss function and should be tuned.
  
  `The larger gamma is, the more conservative the algorithm will be.`
  
  see [details](https://medium.com/data-design/xgboost-hi-im-gamma-what-can-i-do-for-you-and-the-tuning-of-regularization-a42ea17e6ab6)
  
  range: [0,∞]
  
  typical range [0, 20]
  
  __max_depth__ [default=6]
  
  Maximum depth of a tree. Increasing this value will make the model more complex and more likely to overfit. 0 indicates no limit. Note that limit is required when grow_policy is set of depthwise.
  
  `This is the same as the _size of the tree_ parameters *__J__* in GBM (see ESLII, 10.11 Right-Sized Trees for Boosting).`
  
  range: [0,∞]

  textbook suggestion: [4, 8]
  
  typical range: [3, 10] as in [here](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/) 

  Although in many applications J = 2 will be insufficient, it is unlikely that J > 10 will be required. 
  
  Experience so far indicates that `4 < J < 8` works well in the context of boosting, with results being fairly insensitive to particular choices in this range. 

  One can fine-tune the value for J by trying several different values and choosing the one that produces the lowest risk on a validation sample. However, this seldom provides significant improvement over using J = 6.
  
  __min_child_weight__ [default=1]
  
  Minimum sum of instance weight (hessian) needed in a child. If the tree partition step results in a leaf node with the sum of instance weight less than min_child_weight, then the building process will give up further partitioning. In linear regression task, this simply corresponds to minimum number of instances needed to be in each node. The larger min_child_weight is, the more conservative the algorithm will be.

  `Hessian is the H term in objective function of XGBoost; if H is too small,  the obj will be highly determined by the small H term, because H is on the denom side of the obj. So overfitting will happen...`

  range: [0,∞]
  
  Defines the minimum sum of weights of all observations required in a child.

  This is similar to min_child_leaf in GBM but not exactly. This refers to min “sum of weights” of observations while GBM has min “number of observations”.
  
  Used to control over-fitting. Higher values prevent a model from learning relations which might be highly specific to the particular sample selected for a tree.

  Too high values can lead to under-fitting hence, it should be tuned using CV.
  
  
  __max_delta_step__ [default=0]
  
  Maximum delta step we allow each leaf output to be. If the value is set to 0, it means there is no constraint. If it is set to a positive value, it can help making the update step more conservative. Usually this parameter is not needed, but it might help in logistic regression when class is extremely imbalanced. Set it to value of 1-10 might help control the update.
  
  range: [0,∞]
  
  For common cases such as ads clickthrough log, the dataset is extremely imbalanced. This can affect the training of XGBoost model. If you care about predicting the right probability, and in such case, you cannot re-balance the dataset. So set parameter __max_delta_step__ to a finite number (say 1) to help convergence.
  
  `This is basically to add a CAP to detla = y(t) - y(t-1), otherwise if the delta is too big, it will add too much weight to the new learner (tree), and bias the result. So if we add a cap to delta, we can make the model more robust (conservative).`
  
  This parameter is generally not used.
  
  
  __subsample__ [default=1]

  Subsample ratio of the training instances. Setting it to 0.5 means that XGBoost would randomly sample half of the training data prior to growing trees. and this will prevent overfitting. Subsampling will occur once in every boosting iteration.
  
  This is a `boostrapping without replacement` method, and is the same as the GBM subsampling (see ESLII, 10.12.2 Subsampling). It denotes the fraction of observations to be randomly samples for each tree.
  
  range: (0,1]
  
  typical range: [0.5, 1]
  
  
  __colsample_bytree__ [default=1]
  
  Subsample ratio of columns when constructing each tree. Subsampling will occur once in every boosting iteration.
  
  Similar to max_features in GBM. It denotes the fraction of columns to be randomly samples for each tree.

  range: (0,1]
  
  Typical range: [0.5, 1]
  
  
  __colsample_bylevel__ [default=1]
  Subsample ratio of columns for each split, in each level. Subsampling will occur each time a new split is made. `This paramter has no effect when tree_method is set to hist.`
  
  range: (0,1]
  
  Note that __subsample__ + __colsample_bylevel__ = randomForest, and the minor difference is __subsample__ with replacement.
  
  Rarely used because subsample and colsample_bytree will do the job for you.
  
  
  __lambda__ [default=1, alias: reg_lambda]
  
  L2 regularization term on weights. Increasing this value will make model more conservative.


  __alpha__ [default=0, alias: reg_alpha]
   
  L1 regularization term on weights. Increasing this value will make model more conservative.

  These two regularization terms have different effects on the weights; L2 regularization (controlled by the lambda term) encourages the weights to be small, whereas L1 regularization (controlled by the alpha term) encourages sparsity – so it encourages weights to go to 0. This is helpful in models such as `logistic regression`, where you want some feature selection, but in `decision trees` we’ve already selected our features, so zeroing their weights isn’t super helpful. For this reason,setting a high lambda value and a low (or 0) alpha value to be the most effective when regularizing.
  
  In general, set __alpha__ = 0, and only tune __lambda__ in tree model.
  
  __tree_method__ [default= auto]
  
  The tree construction algorithm used in XGBoost. See description in the reference paper.

  Distributed and external memory version only support tree_method=approx.

  Choices: auto, exact, approx, hist, gpu_exact, gpu_hist
  
  auto: Use heuristic to choose the fastest method.
  
        * For small to medium dataset, exact greedy (exact) will be used.
  
        * For very large dataset, approximate algorithm (approx) will be chosen.
        
        * Because old behavior is always use exact greedy in single machine, user will get a message when approximate algorithm is chosen to notify this choice.

  exact: Exact greedy algorithm.

  approx: Approximate greedy algorithm using quantile sketch and gradient histogram.

  [hist](https://medium.com/data-design/xgboosts-new-fast-histogram-tree-method-hist-a3c08f36234c): Fast histogram optimized approximate greedy algorithm. It uses some performance improvements such as bins caching.

  gpu_exact: GPU implementation of exact algorithm.

  gpu_hist: GPU implementation of hist algorithm.
  
  `should set to 'auto'`
  
  __sketch_eps__ [default=0.03]
  
  Only used for tree_method=approx.
  
  This roughly translates into O(1 / sketch_eps) number of bins. Compared to directly select number of bins, this comes with theoretical guarantee with sketch accuracy.

  `Usually user does not have to tune this`. But consider setting to a lower number for more accurate enumeration of split candidates.
  
  range: (0, 1)

  __scale_pos_weight__ [default=1]
  
  Control the balance of positive and negative weights, useful for unbalanced classes. A typical value to consider: `sum(negative instances) / sum(positive instances)`. 
  
  For common cases such as ads clickthrough log, the dataset is extremely imbalanced. This can affect the training of XGBoost model, and there are two ways to improve it. 
  
  If you care only about the overall performance metric (AUC) of your prediction:
  
  --Balance the positive and negative weights via **scale_pos_weight**.

  --Use AUC for evaluation.
    
  range : [1, ∞]
  __updater__ [default= grow_colmaker,prune]
  
  A comma separated string defining the sequence of tree updaters to run, providing a modular way to construct and to modify the trees. This is an advanced parameter that is usually set automatically, depending on some other parameters. However, it could be also set explicitly by a user. The following updater plugins exist:

  `grow_colmaker`: non-distributed column-based construction of trees.

  `distcol`: distributed tree construction with column-based data splitting mode.

  `grow_histmaker`: distributed tree construction with row-based data splitting based on global proposal of histogram counting.

  `grow_local_histmaker`: based on local histogram counting.

  `grow_skmaker`: uses the approximate sketching algorithm.

  `sync`: synchronizes trees in all distributed nodes.
  
  `refresh`: refreshes tree’s statistics and/or leaf values based on the current data. Note that no random subsampling of data rows is performed.

  `prune`: prunes the splits where loss < min_split_loss (or gamma).

  In a distributed setting, the implicit updater sequence value would be adjusted to `grow_histmaker,prune`.

  _This is the internal parameters, in general, no touch on this updater_
  
  
  __refresh_leaf__ [default=1]
  
  Only if `refresh` is used in updater. 
  
  When this flag is 1, tree leafs as well as tree nodes’ stats are updated. When it is 0, only node stats are updated.
  
  __process_type__ [default= default]

  A type of boosting process to run.

  Choices: default, update
   
   `default`: The normal boosting process which creates new trees.

   `update`: Starts from an existing model and only updates its trees. In each boosting iteration, a tree from the initial model is taken, a specified sequence of updater plugins is run for that tree, and a modified tree is added to the new model. The new model would have either the same or smaller number of trees, depending on the number of boosting iteratons performed. Currently, the following built-in updater plugins could be meaningfully used with this process type: `refresh, prune`. With process_type=update, one cannot use updater plugins that create new trees.
   
   If your choice is `update`, then it will only go with `updater = 'refresh, prune'`.
   
   In general, set this to `default`.
   
   __grow_policy__ [default= depthwise]

   Controls a way new nodes are added to the tree.

   Currently supported only if `tree_method = 'hist'`.

   Choices: depthwise, lossguide
   
   `depthwise`: split at nodes closest to the root.

   `lossguide`: split at nodes with highest loss change.
   
   __max_leaves__ [default=0]

   Maximum number of nodes to be added. 
   
   Only relevant when `grow_policy='lossguide'` and `tree_method = 'hist'` is set.
   
   __max_bin__ [default=256]

   Only used if `tree_method = 'hist'`.

   Maximum number of discrete bins to bucket continuous features.

   Increasing this number improves the optimality of splits at the cost of higher computation time.
   
   
   __predictor__ [default=``cpu_predictor``]

   The type of predictor algorithm to use. Provides the same results but allows the use of GPU or CPU.

   `cpu_predictor`: Multicore CPU prediction algorithm.

   `gpu_predictor`: Prediction using GPU. Default when __tree_method__ is `gpu_exact` or `gpu_hist`.
   
# Additional parameters for Dart Booster (booster='dart')
 
# Parameters for Linear Booster (booster='gblinear')
 
# Parameters for Tweedie Regression (objective='reg:tweedie')
 
# Learning Task Parameters
 
 Specify the learning task and the corresponding learning objective. The objective options are below:

 __objective__ [default=reg:linear]

 `reg:linear`: linear regression

 `reg:logistic`: logistic regression

 `binary:logistic`: logistic regression for binary classification, output probability

 `binary:logitraw`: logistic regression for binary classification, output score before logistic transformation

 `gpu:reg:linear`, `gpu:reg:logistic`, `gpu:binary:logistic`, `gpu:binary:logitraw`: versions of the corresponding objective functions evaluated on the GPU; note that like the GPU histogram algorithm, they can only be used when the entire training session uses the same dataset

 `count:poisson` –poisson regression for count data, output mean of poisson distribution
   
    max_delta_step is set to 0.7 by default in poisson regression (used to safeguard optimization)

 `survival:cox`: Cox regression for right censored survival time data (negative values are considered right censored). Note that predictions are returned on the hazard ratio scale (i.e., as HR = exp(marginal_prediction) in the proportional hazard function h(t) = h0(t) * HR).
  
 `multi:softmax`: set XGBoost to do multiclass classification using the softmax objective, you also need to set num_class(number of classes)

 `multi:softprob`: same as softmax, but output a vector of ndata * nclass, which can be further reshaped to ndata * nclass matrix. The result contains predicted probability of each data point belonging to each class.

 `rank:pairwise`: set XGBoost to do ranking task by minimizing the pairwise loss.

 `reg:gamma`: gamma regression with log-link. Output is a mean of gamma distribution. It might be useful, e.g., for modeling insurance claims severity, or for any outcome that might be gamma-distributed.

 `reg:tweedie`: Tweedie regression with log-link. It might be useful, e.g., for modeling total loss in insurance, or for any outcome that might be Tweedie-distributed.

  __base_score__ [default=0.5]
  
  The initial prediction score of all instances, global bias
  
  `For sufficient number of iterations, changing this value will not have too much effect.`

  __eval_metric__ [default according to objective]
  
  Evaluation metrics for validation data, a default metric will be assigned according to objective:
    
    rmse for regression
    
    error for classification
    
    mean average precision for ranking

  User can add multiple evaluation metrics. Python users: remember to pass the metrics in as list of parameters pairs instead of map, so that latter eval_metric won’t override previous one

  The choices are listed below:
    
  `rmse`: root mean square error (regression)

  `mae`: mean absolute error

  `logloss`: negative log-likelihood

  `error`: _Binary classification_ error rate. It is calculated as #(wrong cases)/#(all cases). For the predictions, the evaluation will regard the instances with prediction value larger than 0.5 as positive instances, and the others as negative instances.

  `error@t`: a different than 0.5 _binary classification_ threshold value could be specified by providing a numerical value through ‘t’.

  `merror`: _Multiclass classification_ error rate. It is calculated as #(wrong cases)/#(all cases).
    
  `mlogloss`: Multiclass logloss.

  `auc`: Area under the curve

  `ndcg`: Normalized Discounted Cumulative Gain

  `map`: Mean average precision

  `ndcg@n`, `map@n`: ‘n’ can be assigned as an integer to cut off the top positions in the lists for evaluation.
    
  `ndcg-`, `map-`, `ndcg@n-`, `map@n-`: In XGBoost, `NDCG` and `MAP` will evaluate the score of a list without any positive samples as 1. By adding “-” in the evaluation metric XGBoost will evaluate these score as 0 to be consistent under some conditions.

  `poisson-nloglik`: negative log-likelihood for `Poisson regression`

  `gamma-nloglik`: negative log-likelihood for `gamma regression`

  `cox-nloglik`: negative partial log-likelihood for `Cox proportional hazards regression`

  `gamma-deviance`: residual deviance for `gamma regression`

  `tweedie-nloglik`: negative log-likelihood for `Tweedie regression` (at a specified value of the tweedie_variance_power parameter)
    
  __seed__ [default=0]
  
  Random number seed.    
