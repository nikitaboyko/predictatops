# predictatops

_Code for stratigraphic pick prediction via supervised machine-learning_

Further Information will eventually be in the docs <a href="https://justingosses.github.io/predictatops/html/index.html">here</a>.

[![DOI](https://zenodo.org/badge/151658252.svg)](https://zenodo.org/badge/latestdoi/151658252)

<a title="Triceratops logo based on MathKnight based on photo by Nicholas R. Longrich and Daniel J. Field [CC BY-SA 4.0 (https://creativecommons.org/licenses/by-sa/4.0)], via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:Yale-Peabody-Triceratops-004Trp.png"><img width="512" alt="Yale-Peabody-Triceratops-004Trp" src="docs/Yale-Peabody-Triceratops-004Trp.png"></a>

<a href="https://github.com/JustinGOSSES/predictatops/blob/master/LICENSE">MIT License</a>

This code is the subject of an <a href="https://github.com/JustinGOSSES/predictatops/blob/master/AAPG_Abstract_2019ACE.md">abstract</a> submitted to the AAPG ACE convention in 2019.

Development was in this repo: <a href="https://github.com/JustinGOSSES/MannvilleGroup_Strat_Hackathon">MannvilleGroup_Strat_Hackathon</a> but is now moving here as the code gets cleaned and modulized. This project is under active development. A few portions of the code still only exist on MannvilleGroup_Strat_Hackathon repo at this time. This is a nights and weekend side project, but will continue to be developed by the main developer.

Philosophy
-------

In human-generated stratigraphic correlations there is often talk of lithostratigraphy vs. chronostratigraphy. We propose there is a weak analogy between lithostratigraphy and chronostratigraphy and different methods of computer assisted stratigraphy. 

Historically, many papers that attempted to use code to correlate well logs either assumed there was a mathematical or pattern basis for stratigraphic surfaces that can be teased out of individual logs or one could measured differences in curve patterns between neighboring wells. These workflows share some characteristics with lithostratigraphy in terms of their reliance of matching curve shapes and assumption that changes in lithology are equivelant to stratigraphy, at least at distances equal or greater than well to well distances. 

In contrast, chronostratigraphy assumes lithology can equate to facies belts that can fluctuate gradually in space over time resulting in two wells having similar lithology patterns in different time packages. Traditional chronostratigraphy relies on models of how facies belts should change in space when not otherwise constrained by biostratigraphy, chemostratigraphy, or radiometric dating. 

Instead of relying on stratigraphic models, this project proposes known picks can define spatial distribution of, and variance of, well log curve patterns that are then used to predict picks in new wells. This project attempts to focus on creating programatic features and operations that mimic the low level observations of a human geologist and progressively build into higher order clustering of patterns occuring across many wells that would have been done by a human geologist.

Datasets
-------
The default demo dataset used is a collection of over 2000 wells made public by the Alberta Geological Survey's Alberta Energy Regulator. To quote their webpage, "In 1986, Alberta Geological Survey began a project to map the McMurray Formation and the overlying Wabiskaw Member of the Clearwater Formation in the Athabasca Oil Sands Area. The data that accompany this report are one of the most significant products of the project and will hopefully facilitate future development of the oil sands." It includes well log curves as LAS files and tops in txt files and xls files. There is a word doc and a text file that describes the files and associated metadata. 

_Wynne, D.A., Attalla, M., Berezniuk, T., Brulotte, M., Cotterill, D.K., Strobl, R. and Wightman, D. (1995): Athabasca Oil Sands data McMurray/Wabiskaw oil sands deposit - electronic data; Alberta Research Council, ARC/AGS Special Report 6._

Please go to the links below for more information and the dataset:

Report for Athabasca Oil Sands Data McMurray/Wabiskaw Oil Sands Deposit http://ags.aer.ca/document/OFR/OFR_1994_14.PDF

Electronic data for Athabasca Oil Sands Data McMurray/Wabiskaw Oil Sands Deposit http://ags.aer.ca/publications/SPE_006.html Data is also in the repo folder: SPE_006_originalData of the original repo for this project <a href="https://github.com/JustinGOSSES/MannvilleGroup_Strat_Hackathon/tree/master/SPE_006_originalData">here.</a>

In the metadata file <a href="https://github.com/JustinGOSSES/MannvilleGroup_Strat_Hackathon/blob/master/SPE_006_originalData/Metadata/SPE_006.txt">SPE_006.txt</a> the dataset is described as `Access Constraints: Public` and `Use Constraints: Credit to originator/source required. Commercial reproduction not allowed.`

_The Latitude and longitude of the wells is not in the original dataset._ <a href="https://github.com/dalide">@dalide<a> used the Alberta Geological Society's UWI conversion tool to find lat/longs for each of the well UWIs. A CSV with the coordinates of each well's location can be found <a href="https://github.com/JustinGOSSES/MannvilleGroup_Strat_Hackathon/blob/master/well_lat_lng.csv">here.</a> These were then used to find each well's nearest neighbors.

Please note that there are a few misformed .LAS files in the full dataset, so the code in this repository skips those.

If for some reason the well data is not found at the links above, you should be able to find it <a href="https://github.com/JustinGOSSES/MannvilleGroup_Strat_Hackathon/tree/master/SPE_006_originalData">here.</a>


Architecture and Abstraction
-------
Although very much a work in progress, I've tried to organize things such that the whole process doesn't have to be done at once and intermediate work can be easily saved to file and the work started at a later date. This was done running the full sequence of tasks to completion can take several hours, and I typically had less time than that to work on this project. I suspect others will have time limits as well.

This project is made up of a series of self-contained tasks done in series. The results from one task can be optionally saved to file and then reloaded into memory at a later time before starting the next task. Also, a task can be run x different ways and results saved in x different files which are then used x different times by the later steps.

In terms of how much the code is abstracted into higher level actions vs small lower level actions, I'm trying to enable two levels of abstraction. At one level, individual arguments are supplied to functions by the users who then calls those functions, potentially in a Jupyter Notebook. Many functions are called for each task. I'm also trying to enable a higher order way of working in which all arguments are configuration options set in configuration files before any code runs. This gives less visability into what is happening, but allows one to set up a bunch of different configuration variables, write a script to run all of them through to completion in the cloud, walk away, come back hours later, and evaluate what options work better for their particular datasets.  


### Code Tasks
The code is broken into individual Tasks. 
Mandetory ones will have (m). Option ones denoted by a (o). 

- (m) [main] Main.py function. Used for some utilities leveraged across multiple steps.
- (o) [fetch_demo_data.py] The full example dataset is quite large, so it is kept in a separate folder that won't be added to PyPy (eventually this will be there maybe). Instead, we fetch it once via this script, which leverages Pooch.
- (m) [configurationplusfiles] Sets input path, configuration, and output path variables used by the Predictatops
- (o) [checkdata] Counts combinations of available tops and curves to help users figure out what wells can be used.
- (m) [load] Loads LAS files based on a well list constructed in the checkdata step.
- (m) [split] Splits the wells from load into train or test wells and assigns labels. Need to do before feature creation due to some features using neighboring wells. You don't want to use test well information when creating features for training.
- (m) [wellsKNN] Find K nearest neighbors for each well. Creates features based on neighbor relationships.
- (m) [features] Create additioal features.
- (m) [balance] Deal with imbalanced class distribution by duplicating some rows and taking out very common varieties.
- (m) [trainclasses] Machine learning 1: Model training
- (m) [predictionclasses] Go from trained model from trainclasses.py to predicted classes at each depth point.
- (o) [traintops] Secondary Machine learning 2 that takes results of class prediction and uses a secondary machine learning model to predict the top based on regression. : Inference
- (o) [predictiontops] Uses models in traintops and predict the top through regression & and a limited set of features instead of heuristic set of rules.
- (o) [plot] Map & plot results.
- (o) [uncertainty] Potential places for functions for calculating uncertainty predictions and plotting ranges.

Each task has at least one .py file with low level functions and another higher level .py file that calls those functions, often with the same name but _runner appended. 

An example is `load.py` and `load_runner.py`. 

The higher level .py file imports the results of configurationplusfiles.py to get variables for configuration, input file locations, and output saved files locations. It also calls the functions in the lower level .py file. The lower level .py files hold functions, nothing is run by them when the file is run. For example, in a command line `python3 load.py` won't do anything. `python3 load_runner.py` will execute code. 

Alternatively to using the higher level .py files, just the lower level .py files can be called and work of the _runner files done in the cells of a jupyter notebook.

I've followed this breakdown as I wanted to both train together things like configurationplusfiles_runner.py load_runner.py and split_runner.py easily while also easily ignoring them altogether as different methods are substituted. The goal was to allow code to be swapped out easily without having to keep track of much as the code is still rapidly changing while also be able to set up different trials to run in sequence. 

Folder Structures
-------
- <b>predictatops</b> = These are the source files. I.E. the actual code.
- <b>Data</b> = Where the data input goes. 
- <b>Demo</b> = I'll put some .py files and Jupyter Notebooks here that demo how to run the code.
- <b>Docs</b> = Documentation will go here, eventaully.
- <b>Results</b> = Intermediate and final results will be written by default to directories and files inside this directory as established in the output function of configurationplusfiles.py.
- <b>Tests</b> = Place to put code the runs tests.

GettingStarted
-------
see <a href="https://justingosses.github.io/predictatops/html/index.html">docs</a>

### Installation
see <a href="https://justingosses.github.io/predictatops/html/index.html">docs</a>

How to use
-------
Multiple options
- Changing configuration, input data, and output data variables.
- runner python files vs. function and imports only python files
- running incrementally in jupyter notebook vs. bash pipeline in bulk
But see <a href="https://justingosses.github.io/predictatops/html/index.html">docs</a>


### Code features that might require changing for different datasets
- Requires all wells to be in the same file format, LAS.
- The load_all_wells_in() function in load.py does a slight transformation to the UWI names as the UWI uses / in places and - in others. Please look at this function and your datasets to figure out if it applies to your data. You may need to modify this piece of code for your own purposes.
- There is an assumption that the picks text file includes a quality column. If there isn't, fake one with everything as equally quality at value of 

Credits
-------

#### Contributors

Triceratops image by <a href="https://commons.wikimedia.org/wiki/File:Yale-Peabody-Triceratops-004Trp.png"><img width="512" alt="Yale-Peabody-Triceratops-004Trp">MathKnight based on photo by Nicholas R. Longrich and Daniel J. Field</a> [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0) 
<a href="https://github.com/JustinGOSSES">Justin Gosses</a>, <a href="https://github.com/dalide">Licheng Zhang</a>, <a href="https://github.com/jazzskier">jazzskier</a>

Project originally started as <a href="http://www.agilegeoscience.com/">Agile Scientific</a> Hackathon project, September 24th, 2017. Original work is in another repository on github <a href="https://github.com/JustinGOSSES/MannvilleGroup_Strat_Hackathon">here<a/>. This work has benefited from conversations on the integration of machine-learning and subsurface domains on the <a href="https://softwareunderground.org/">SWUNG</a> Slack channel. 


#### Key Dependencies
This package was created with <a href="https://github.com/audreyr/cookiecutter">Cookiecutter</a> and the <a href="https://github.com/audreyr/cookiecutter-pypackage">`audreyr/cookiecutter-pypackage`_</a> project template.

Libraries used for working with well logs include: <a href="https://github.com/kinverarity1/lasio">Lasio</a> & <a href="https://github.com/search?q=welly">Welly</a>.


-------------------------------------------
Status
-------
Some of the work is still in <a href="https://github.com/JustinGOSSES/MannvilleGroup_Strat_Hackathon">the old repository</a>, but it is progressively being moved here and repackaged out of Jupyter notebooks and into an actual package.

## Recent updates
The code runs faster and and mean absolute error is down from 90 to 15.03 and now ~7 (with a handful of wells identified as too difficult to predict, -8% depending on the run and settings. 

Key approaches were:
1. Leverage knowledge from nearby wells.
2. Instead of distinguishing between 2 classes, pick and not pick, distinguish between 3 classes: (a) pick, (b) not pick but within 3 meters and (c) not pick and not within 3 meters of pick.
3. More features
4. A Two step approach to machine-learning: 

- 4.1. First step is class-based prediction. Classes are groups based on distance from actual pick. For example, depths at the pick, depths within 0.5 meter, depths within 5 meters above, etc. 
- 4-2. Second step is more concerned with picking between the depths predicted as being in the classes nearest to the pick. We've explored both a rule-based scoring and a regression machine-learning process for this. 
- 4.2.1. The rule-based approach uses the class prediction and simple additive scoring of the class predictions based across different size windows. In a scenario where there are two depths with a predicted class of 100, we decide between them by adding up all the class scores across different size windows above and below each depth. The depth with the highest aggregate score wins  and it declared the "predicted depth". We take this route as we assume the right depth will have more depths near it that look like the top pick and as such have higher classes predicted for depths around it while false positives will be more likely to have more lower level classes around it.
- 4.2.2. We're also trying regression-based machine-learning to predict the distance from each depth in question to the actual pick. The depth with the lowest predicted distance between it and actual pick is chosen as the "predicted pick". This approach hasn't given any better results than the simple rule-based aggregate scoring.
 

#### Distribution of Absolute Error in Test Portion of Dataset for Top McMurray Surface in Meters. 
Y-axis is number of picks in each bin, and X-axis is distance predicted pick is off from human-generated pick.
<img src="demo/current_errors_TopMcMr_20181006.png"
     alt="image of current_errors_TopMcMr_20181006"
     style="float: left; margin-right: 25px;" />

Current algorithm used is XGBoost.

## Future Work [also see issues]
7. Visualize probabilty of pick along well instead of just returning max probability prediction in each well. 
8. Generate average aggregate wells in different local areas for wells at different prediction levels. See if there are trends or if this helps to idenetify geologic meaningful features that correlate to many combined machine-learning model features. 
9. Explore methods to visualize weigtings of features on individual well basis using techniques similar to those learned in image-based deep-learning. 
10. Cluster wells using unsupervised learning and then see if clusters can be created that correlated with supervised prediction results. (initial trials with UMAP give encouraging results)
11. Rework parts of this into more object oriented approach.
12. Use H2O's automl library to try to improve on standard XGBoost approach.