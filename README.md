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
