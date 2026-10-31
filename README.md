# Python-Assignment-DLMDSPWP01
Author :Mahaboobvali_Shaik_4242671_DLMDSPWP01




Ideal Function Selection and Test Data Mapping

A Python program, written for the IU course DLMDSPWP01 – Programming with Python. It uses training data to choose the four best-fitting ideal functions out of fifty, using the least-squares criterion. It then assigns test data points to those functions using a √2 deviation tolerance.

Overview

The program works through the analysis in this order:

Loads and validates three CSV files: training data, ideal functions and test data.
Selects four ideal functions. For each training function (y1–y4), it picks the ideal function (out of y1–y50) with the lowest mean squared error (MSE). This gives the same choice as minimising the sum of squared deviations.
Computes the maximum deviation between each training function and the ideal function chosen for it.
Maps each test point. A test point is assigned to a chosen ideal function only if its deviation is at most √2 × that maximum deviation. If several functions qualify, the one with the smallest deviation wins.
Stores everything in an SQLite database (assignment.db) via SQLAlchemy.
Visualises the training data and the selected ideal functions with Bokeh.
Verifies the core functions with 18 unit tests.
Results
Training function	Selected ideal function	MSE	Max deviation	Threshold (√2 × max)
y1	y42	0.0856	0.4960	0.7014
y2	y41	0.0890	0.4977	0.7039
y3	y11	0.0747	0.4989	0.7056
y4	y48	0.0799	0.4997	0.7067

Of the 100 test points, 48 were assigned: 13 to y41, 12 to y42, 12 to y11 and 11 to y48. The remaining 52 fell outside every tolerance band and were left unassigned.

Project Structure
.
├── improved_assignment.ipynb   # Main notebook: full pipeline and unit tests
├── train.csv                   # Training data: x, y1–y4 (400 rows)
├── ideal.csv                   # Ideal functions: x, y1–y50 (400 rows)
├── test.csv                    # Test data: x, y (100 rows)
├── assignment.db               # SQLite database, created on run
└── README.md

The CSV datasets are provided individually by IU and may not be included in this repository. Place them in the project root before running.

Requirements
Python 3.10 or newer (developed with Python 3.12)
pandas
numpy
scikit-learn
SQLAlchemy
bokeh (optional; the plotting cells are skipped if it is not installed)
jupyter

Install the dependencies with:

bash
pip install pandas numpy scikit-learn sqlalchemy bokeh jupyter
Usage
Clone the repository:
bash
   git clone --branch develop https://github.com/<user>/<repo>.git
   cd <repo>
Copy train.csv, ideal.csv and test.csv into the project folder.
Start Jupyter and open the notebook:
bash
   jupyter notebook improved_assignment.ipynb
Run all cells (Kernel → Restart & Run All). The notebook will:
print the selected ideal functions and their maximum deviations
display the mapping results and the count of points per function
create assignment.db with all tables
show the Bokeh plots
run the unit tests
Database Tables
Table	Columns	Description
training_data	x, y1–y4	The four training functions
ideal_data	x, y1–y50	The fifty ideal functions
test_data	x, y	The raw test points
mapped_results	x, y, deviation, chosen_ideal_function	Mapping result for every test point (NULL if not assigned)
mapping_counts	chosen_ideal_function, count	Number of test points per ideal function
Main Functions
Function	Purpose
load_csv()	Loads a CSV file and checks it exists, parses, is non-empty and has the required columns
find_best_fit_functions()	Picks the ideal function with the lowest MSE for each training function
find_max_deviations()	Computes the largest absolute deviation for each selected pair
map_test_data()	Assigns test points using the √2 tolerance rule
count_mapped_points()	Summarises how many test points went to each function
get_engine(), save_to_db(), load_from_db()	SQLite access through SQLAlchemy
Error Handling

The program defines five custom exceptions, all subclasses of Exception:

DataLoadError – file missing or cannot be parsed
MissingColumnError – a required column is absent
EmptyDataError – a dataset contains no rows
DatabaseError – a database read or write fails
MappingError – a mapping or deviation step cannot be completed

Errors raised by libraries are caught and re-raised as these exceptions with raise ... from exc, so the original traceback is kept. Every main step is also recorded with Python's logging module.

Testing

The last cell of the notebook runs 18 unit tests, grouped into six unittest.TestCase classes:

Test class	Tests	What it checks
TestDataLoading	3	Missing file, missing column, valid file
TestFindBestFit	4	Correct keys, valid column names, error cases
TestFindMaxDeviations	2	Correct deviation, missing column
TestMapTestData	4	Matched points, unmatched x, error cases
TestDatabase	3	Save and reload, empty data, append mode
TestCountMappedPoints	2	Correct counts, missing column

The tests use small synthetic datasets with known answers, so they do not depend on the assignment CSV files.

Contributing

The team integrates its work on the develop branch:

bash
git checkout develop
git pull origin develop
git checkout -b feature/<feature-name>

# make your changes
git add .
git commit -m "Describe your change"
git push -u origin feature/<feature-name>

Then open a pull request into develop. Your change is merged once a team member has reviewed and approved it.

Possible Improvements
Move the data handling into classes (e.g. Dataset → TrainingData, IdealFunctions, TestData)
Match x-values with np.isclose instead of exact floating-point equality
Check explicitly that training and ideal data share the same x-values
Add a plot of the test points against each ideal function's tolerance band
Author
