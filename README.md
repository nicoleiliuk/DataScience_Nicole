# Bio 835AQ - Bat / Non-bat Acoustic Classification Pipeline

A reproducible proof-of-concept workflow for screening acoustic
recordings as BAT, NON-BAT, or EXPERT REVIEW using a Random
Forest classifier.

The project contains two Google Colab notebooks:

DS_Training_Pipeline.ipynb - preprocesses labelled recordings,
extracts acoustic features, trains and evaluates the Random Forest
classifier, and exports the final model bundle.

DS_Prediction_Tool.ipynb - loads the trained model bundle and
applies the same preprocessing and feature-extraction workflow to
new .wav recordings.

The classifier is intended as a screening tool, not as a replacement
for expert acoustic identification. Its purpose is to reduce the number
of recordings requiring manual inspection while minimizing the risk of
automatically discarding true bat recordings.


# Requirements

The notebooks were designed for Google Colab and use Google Drive
for project storage.

Main Python (version 3.12.13) packages:

- numpy (version 2.0.2)

- pandas (version 2.2.3)

- scikit-learn (version 1.6.1)

- librosa (version 0.11.0)

- soundfile (version 0.14.0)

- matplotlib (version 3.10.0)

- joblib (version 1.5.3)

- tqdm (version 4.67.3)

The training notebook prints the Python, NumPy, pandas, and scikit-learn
versions so that the computational environment can be documented when
the analysis is run.

# Suggested Google Drive structure

The training notebook currently uses:

MyDrive/bat_project/ → bat_audio_metadata_annotated.csv | audio/ (WAV recordings) | outputs/ (trained model and generated outputs)

If the project is stored elsewhere, change only:

PROJECT_DIR = Path("/content/drive/MyDrive/bat_project")

and keep the remaining paths relative to PROJECT_DIR.

# Important model-path note

The current training notebook exports:

bat_project/outputs/bat_classifier_model.joblib

The prediction notebook must point to this same exported file. If the
prediction notebook is configured to use a models/ directory or a
different filename, update MODEL_PATH accordingly or move/rename the
exported bundle before running predictions.

For example:

MODEL_PATH = PROJECT_DIR / "outputs" / "bat_classifier_model.joblib"

# Metadata

The training notebook expects a CSV containing the labelled recordings
and associated metadata.

Fields used by the current workflow include:

- filename

- classification

- recording_group

- recorder

- date

- source

classification is the binary target and should contain:

- bat
- non-bat

Metadata columns are retained for traceability and validation but are
not used as acoustic predictors.

The notebook checks that every filename in the metadata table exists in
the audio directory before model training.

# Confidence-based screening

The prediction workflow uses three categories instead of forcing every
recording into a binary decision.

P(bat) <= 0.30           NON-BAT

0.30 < P(bat) < 0.70     EXPERT REVIEW

P(bat) >= 0.70           BAT

The thresholds prioritize retention of true bat recordings.

# Using the prediction tool

-Run DS_Prediction_Tool.ipynb in Google Colab;

-Mount Google Drive;

-Confirm that MODEL_PATH points to the model bundle exported by the
training notebook;

-Run the preprocessing and feature-extraction cells;

-Upload one or more .wav recordings;

-Run the classification cell;

-Inspect probability_bat and review_status;

-Download predictions.csv.



The output contains:

*filename* - Name of the uploaded WAV file

*model_prediction* - Standard binary Random Forest prediction

*probability_bat* - Random Forest probability assigned to the bat class

*review_status* - recommended screening output.

Files that cannot be processed are recorded separately in
prediction_errors.csv.


# Interpretation

BAT

The model assigned P(bat) >= 0.70. The recording should be retained as
a high-confidence bat candidate.

NON-BAT

The model assigned P(bat) <= 0.30. The recording is considered a
high-confidence non-bat candidate.

EXPERT REVIEW

The model assigned an intermediate probability (0.30 < P(bat) < 0.70).
The recording should be manually inspected rather than automatically
discarded.

Random Forest probabilities are used as practical confidence scores and
should not be interpreted as formally calibrated probabilities of
biological certainty.

# Reproducibility checklist

For a reproducible run:

- use the same labelled audio dataset and metadata;

- preserve filenames, class labels, recorder information, and
recording groups;

- run notebook cells sequentially in a fresh Colab session;

- keep random_state=42;

- use the same 256 kHz target sample rate;

- keep preprocessing identical between training and prediction;

- keep feature definitions and parameters unchanged;

- preserve the exact feature-column order stored in the model bundle;

- use the same Random Forest hyperparameters, including 200 trees;

- document package versions;

- keep the 0.30/0.70 screening thresholds with the saved model;

- re-evaluate the model and thresholds after meaningful changes to the
dataset or pipeline.

# Intended use

This project was developed as a proof of concept for binary acoustic
screening of bat and non-bat recordings. It is suitable for
demonstrating a reproducible machine-learning workflow for passive
acoustic monitoring.

It should not be treated as a validated universal bat detector.
Application to new recording systems or environments requires additional
labelled data and independent evaluation.
