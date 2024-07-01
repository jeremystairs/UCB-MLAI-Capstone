## Capstone Project: Predicting Speeding from Open Street Maps and Telematics Data

**Professional Certificate in Machine Learning and Artificial Intelligence**<br>
**Jeremy Stairs**<br>
**June 2024**
<br>
<br>

### Executive summary
This project uses Open Street Maps (OSM) data with fleet vehicle data to predict if a driver is speeding in each driving report. Using OSM data, the cost per report will be much lower than the paid mapping services. Unfortunately, OSM is far from complete. Many requests are returned with no speed limit. It's difficult to predict the speed limit in a specific location using just OSM data. However, we might be able to use OSM data along with the known vehicle speed to predict if the driver is speeding. It's unlikely that the error will be low enough to reliably send an alert to the user. Still, this prediction can idicate potential speeding. If speeding is likely, we can perform more accurate and costly speed limit queries. Ultimately, it's important that we catch each instance of speeding to ensure driver safety for all of our customers.
<br>
<br>

### Rationale
As a fleet telematics company, our customers reqire alerts when drivers are speeding. Vehicle speed is included in each vehicle report. However, there are few complete speed limit databases. None are free. The Open Street Map project includes speed limits, but this only covers about 12% of its roads. Until the crowd-sourced database is complete, a model for predicting when a driver is speeding would allow us to reduce costs while still providing our customers with alerts needed to ensure fleet vehicles are driven safely.
<br>
<br>

### Research Question
Can telematics data and Open Street maps data be used to determine if a driver is speeding?

Note: My original question asked if the speed limit could be predicted from OSM data. Early testing showed scores under 0.3. This was possibly due to the target being multilabel with over 10 different labels. I decided to refoucus the question to determine when vehicles were driven over the posted speed limit. Thas has a simiple yes/no target and is very relavent to our company's speed limit alert serivce.
<br>
<br>

### Data Sources
This project has two data sources:

Fleet telematics data
Open Street Maps (OSM) tag data
Speed limits and associated mapping data are publicly available from the Open Street Map (OSM) project. There are mapping files with varying levels of granularity (continent, country, state). The files can be uncompressed and imported to a PostgreSQL server. The compressed mapping files for the entire world are about 100 GB which require about 1TB of database space. We have a server with full mapping data for the continental US.

OSM Wiki: https://wiki.openstreetmap.orgLinks to an external site.

OSM mapping files: https://download.geofabrik.de/

Our units in the field report telematics data periodically while moving. This data is combined with data from OSM based on the vehicle location. I extracted data for several days of reporting when both vehicle speed and OSM speed limit were included. The data was unaltered expect for the 'speeding' column which was derived from the posted speed limit and the vehicle speed.

The dataset contains eight features and one target:

- normalizedSpeed - vehicle speed normalized to miles per hour
- lanes - number of lanes traveling in the same direction
- oneway - indicates if the OSM way only allows travel in one directions, possible values: yes, no, reversible, alternating
- surface - the phyical surface of the OSM way
- hgv - indicates conditions for heavy good vehicles
- foot - indicates if pedestrian foot traffic is allowed
- horse - indicates if equestrians are allowed
- bicycle - indicates if bicyclists are allowed
- speeding - target - if 'yes', the vehicle speed was greater than posted speed limit, otherwise 'no'

<img src="images/target_balance.png" alt="drawing" style="width:400px;"/>
<br>

### Methodology
Overall, the project follows the Data Science Life Cycle (DSLC).

**Data Cleaning**<br>
Feature examination exposed several changes needed during data cleaning:
- 'lanes' will be cast from an object to a numerical feature
- 'oneway' will have the values '-1' changed to 'reversible'
- 'horse' will have the value 'no '' converted to 'no'
- the features 'oneway', 'surface', and 'hgv' will be converted to numerical features using one-hot encoding
- the features 'foot', 'horse', and 'bicycle' will be converted to numerical features using binary encoding

**Modeling**<br>
In the modeling phase, four classifiers are used:

- Logistic Regression
- K-Nearest Neighbors
- Decision Tree
- Support Vecor Machine

A baseline will be set using DummyClassifier and LogisticRegression with default settings. Further modeling will use the classifiers above with default parameters. Light hyperparameter tuning will be done using grid search. Future work will include more thorough hyper parameter tuning with different scoring metrics.
<br>
<br>

### Results
Initial testing shows the shows positive results. A variety of classifiers along with hyperparameter tuning shows improvement in accuracy scores.

The baseline score with the dummy classifier was 0.6415. 

#### Default model performance:

Model | Training Time (ms) | Training Accuracy | Test Accuracy
---:|:---:|:---:|:---
**Logistic Regression** | 698 | 0.7817 | 0.7858
**K-Nearest Neighbors** | 11.0 | 0.8376 | 0.7525
**Decision Tree** | 37.0 | 0.8658 | 0.7043
**Support Vector Machine** | 3951 | 0.7812 | 0.7899

Logistic regression and SVC had very close scores, but the train time for logistic regression was much faster.

#### Tuned model performance:

Model | Training Time (ms) | Training Accuracy | Test Accuracy
---:|:---:|:---:|:---
**Logistic Regression** | 40 | 0.7817 | 0.7862
**K-Nearest Neighbors** | 11 | 0.8251 | 0.7769
**Decision Tree** | 23 | 0.7980 | 0.8034
**Support Vector Machine** | 5006 | 0.7827 | 0.7903

With light hyperparameter tuning using grid search, the decision tree has the best score.
<br>
<br>

### Next steps
The test scores show that ML modeling is a viable way to predict events where the driver is over the posted speed limit. Further imporvements to the models should increase accuracy.

Future work will use grid search to more thoroughly tune hyperparameters. It will also consider scoring metrics to reduce false negatives or false positives for different business cases. We need to determine if the predictions should favor a lower the chance of missing a speeding event or a lower chance of sending a false speeding alert. This will be a business decision, but it will affect the scoring method used to optimize the models.
<br>
<br>

### Outline of project
Detailed finding can be found in this Jupyter Notebook: [capstone.ipynb](https://github.com/jeremystairs/UCB-MLAI-Capstone/blob/main/capstone.ipynb)

Initial data: [data](https://github.com/jeremystairs/UCB-MLAI-Capstone/tree/main/data)

Images generated during analysis: [images](https://github.com/jeremystairs/UCB-MLAI-Capstone/tree/main/images)
<br>
<br>

### Contact and Further Information
Please contact me through the comments section of this project.