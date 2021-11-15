## Regression (3)

| Short Description | Function |
|---|---|
| Apply the result of a linear regression | `model.reg.linear` |
| Propagate uncertainties through a linear regression  | `model.reg.linearVariance` | 
| Fit and predict a Gaussian Process  | `model.reg.gaussianProcess` | 

## Decision and Regression Trees (7)

| Short Description | Function |
|---|---|
| All-in-one function for simplest case  | `model.tree.simpleTree` | 
| Simple test function for a tree node  | `model.tree.simpleTest` | 
| Test function for a tree node with logical operators  | `model.tree.compoundTest` |  
| Test function with missing value handling  | `model.tree.missingTest` | 
| Chain of surrogate tests | `model.tree.surrogateTest` |
| Tree walk without explicit missing value handling  | `model.tree.simpleWalk` | 
| Tree walk with three branches: pass, fail, and missing  | `model.tree.missingWalk` |

## Cluster Models (5)

| Short Description | Function |
|---|---|
| Closest cluster | `model.cluster.closest` | 
| Closest *N* clusters or N-nearest neighbours  | `model.cluster.closestN` | 
| Random seeds for online clustering  | `model.cluster.randomSeeds` | 
| Online clustering with k-means  | `model.cluster.kmeansIteration` | 
| Update cluster using the mean of data points  | `model.cluster.updateMean` | 

## Nearest Neighbor Models (3)

| Short Description | Function |
|---|---|
| K nearest points | `model.neighbor.nearestK` |
| All points within R | `model.neighbor.ballR` | 
| Mean of a sample of points, with weights | `model.neighbor.mean` | 

## Naive Bayes (3)

| Short Description | Function |
|---|---|
| Bernoulli two-category likelihood  | `model.naive.bernoulli` | 
| Multinomial multi-category likelihood  | `model.naive.multinomial` | 
| Gaussian continuous likelihood  | `model.naive.gaussian` | 

## Neural Networks (1)

| Short Description | Function |
|---|---|
| Feedforward neural network organized in layers  | `model.neural.simpleLayers` |  

## Support Vector Machines (1)

| Short Description | Function |
|---|---|
| Basic SVM | `model.svm.score` |
