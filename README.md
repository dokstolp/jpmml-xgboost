JPMML-XGBoost
=============

Java library and command-line application for converting [XGBoost](https://github.com/dmlc/xgboost) models to PMML.

# Prerequisites #

* Java 1.7 or newer.

# Installation #

Enter the project root directory and build using [Apache Maven](http://maven.apache.org/):
```
mvn clean install
```

The build produces an executable uber-JAR file `target/converter-executable-1.2-SNAPSHOT.jar`.

# Usage #

A typical workflow can be summarized as follows:

1. Use XGBoost to train a model.
2. Save the model and the associated feature map to files in a local filesystem.
3. Use the JPMML-XGBoost command-line converter application to turn those two files to a PMML file.

### The XGBoost side of operations

Using [`r2pmml`](https://github.com/jpmml/r2pmml) and [`xgboost`](http://cran.r-project.org/web/packages/xgboost/) packages to train a linear regression model for the example `mtcars` dataset:
```R
library("r2pmml")
library("xgboost")

data(mtcars)

# Convert selected columns from numeric datatype to integer or factor datatypes
mtcars$cyl = as.integer(mtcars$cyl)
mtcars$vs = as.factor(mtcars$vs)
mtcars$am = as.factor(mtcars$am)
mtcars$gear = as.integer(mtcars$gear)
mtcars$carb = as.integer(mtcars$carb)

mpg_y = mtcars[, 1]
mpg_X = mtcars[, 2:ncol(mtcars)]

# Generate feature map
mpg.fmap = r2pmml::genFMap(mpg_X)
r2pmml::writeFMap(mpg.fmap, "xgboost.fmap")

# Generate DMatrix
mpg.dmatrix = r2pmml::genDMatrix(mpg_y, mpg_X, "xgboost.svm")

set.seed(31)

# Train a linear regression model
mpg.xgb = xgboost(data = mpg.dmatrix, objective = "reg:linear", nrounds = 7)

# Save the model in XGBoost proprietary binary format
xgb.save(mpg.xgb, "xgboost.model")

# Dump the model in text format
xgb.dump(mpg.xgb, "xgboost.model.txt", fmap = "xgboost.fmap")
```

### The JPMML-XGBoost side of operations

Converting the model file `xgboost.model` together with the associated feature map file `xgboost.fmap` to a PMML file `xgboost.pmml`:
```
java -jar target/converter-executable-1.2-SNAPSHOT.jar --model-input xgboost.model --fmap-input xgboost.fmap --target-name mpg --pmml-output xgboost.pmml
```

Getting help:
```
java -jar target/converter-executable-1.2-SNAPSHOT.jar --help
```

# License #

JPMML-XGBoost is licensed under the [GNU Affero General Public License (AGPL) version 3.0](http://www.gnu.org/licenses/agpl-3.0.html). Other licenses are available on request.

# Additional information #

Please contact [info@openscoring.io](mailto:info@openscoring.io)
