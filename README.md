# Bio 835AQ - Bat / Non-bat Acoustic Classification Pipeline

A reproducible proof-of-concept workflow for screening acoustic
recordings as BAT, NON-BAT, or EXPERT REVIEW using a Random
Forest classifier.

The project contains two Google Colab notebooks:

DS_Training_Pipeline.ipynb --- preprocesses labelled recordings,
extracts acoustic features, trains and evaluates the Random Forest
classifier, and exports the final model bundle.

DS_Prediction_Tool.ipynb --- loads the trained model bundle and
applies the same preprocessing and feature-extraction workflow to
new .wav recordings.

The classifier is intended as a screening tool, not as a replacement
for expert acoustic identification. Its purpose is to reduce the number
of recordings requiring manual inspection while minimizing the risk of
automatically discarding true bat recordings.

Workflow

Labelled WAV recordings + metadata
                |
                v
      Minimal preprocessing
                |
                v
   Acoustic feature extraction
        (52 predictors)
                |
                v
       Random Forest model
          (200 trees)
                |
                v
      Model evaluation
                |
                v
   Confidence-based screening
       /        |         \
 NON-BAT   EXPERT REVIEW   BAT
 P <= .30    .30-.70      P >= .70

The trained model bundle is then used by the prediction notebook:

New WAV recordings
        |
        v
Same preprocessing
        |
        v
Same 52 acoustic features
        |
        v
Saved Random Forest model
        |
        v
P(bat) + screening category
        |
        v
predictions.csv

Requirements

The notebooks were designed for Google Colab and use Google Drive
for project storage.

Main Python packages:

numpy

pandas

scikit-learn

librosa

soundfile

matplotlib

joblib

tqdm

The training notebook prints the Python, NumPy, pandas, and scikit-learn
versions so that the computational environment can be documented when
the analysis is run.

Suggested Google Drive structure

The training notebook currently uses:

MyDrive/
└── bat_project/
    ├── bat_audio_metadata_annotated.csv
    ├── audio/
    │   ├── recording_001.wav
    │   ├── recording_002.wav
    │   └── ...
    └── outputs/

If the project is stored elsewhere, change only:

PROJECT_DIR = Path("/content/drive/MyDrive/bat_project")

and keep the remaining paths relative to PROJECT_DIR.

Important model-path note

The current training notebook exports:

bat_project/outputs/bat_classifier_model.joblib

The prediction notebook must point to this same exported file. If the
prediction notebook is configured to use a models/ directory or a
different filename, update MODEL_PATH accordingly or move/rename the
exported bundle before running predictions.

For example:

MODEL_PATH = PROJECT_DIR / "outputs" / "bat_classifier_model.joblib"

Metadata

The training notebook expects a CSV containing the labelled recordings
and associated metadata.

Fields used by the current workflow include:

filename

classification

recording_group

recorder

date

source

classification is the binary target and should contain:

bat
non-bat

Metadata columns are retained for traceability and validation but are
not used as acoustic predictors.

The notebook checks that every filename in the metadata table exists in
the audio directory before model training.
