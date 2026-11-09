# Facial Expression Classification Demo

A webcam-based computer vision project built by **Abhishek Yadav** and **Chitrakant Verma** as a fifth-semester minor project. It detects the largest face in a frame and displays a model-predicted facial expression label, confidence score, processing time and recent predictions. These labels describe visible patterns; they do not establish how a person feels.

**Stack:** Python · Flask · OpenCV · ONNX · HTML/CSS/JavaScript  
**Inspect:** [web app](app.py) · [training workflow](train.py) · [held-out evaluation](evaluate.py) · [tests](tests/)

## What we built

- A browser interface that captures webcam frames with permission and sends one JPEG frame at a time to the Flask prediction endpoint.
- Face detection and expression classification for the largest detected face. The UI shows confidence, processing time and a browser-only recent-prediction history.
- Input checks for frame type, dimensions and size, plus error handling and privacy-related response headers.
- A separate six-class FER-2013 CNN training workflow and an evaluation script that writes accuracy, macro F1, per-class metrics and a confusion matrix on the held-out PrivateTest split.

## Model provenance and results

The runnable app uses the **pretrained FER+ ONNX model** from the [ONNX Model Zoo](https://github.com/onnx/models/tree/main/validated/vision/body_analysis/emotion_ferplus). We did not train that pretrained model. [Model attribution](models/) is included in the repository; [download_model.py](download_model.py) checks the weight file against a SHA-256 manifest.

The separate training script can train a six-class CNN on a locally obtained FER-2013 CSV. It uses Training for fitting, PublicTest for validation and PrivateTest for final evaluation. **No accuracy or cross-dataset score is claimed here:** this repository does not include a reproducible completed run or its metrics. Six-class results are not directly comparable with seven- or eight-class benchmarks.

## Run the app locally

Use Python 3.12. From the repository root on Windows PowerShell:

~~~powershell
py -3.12 -m venv .venv
.venv\Scripts\python.exe -m pip install -r requirements.txt
.venv\Scripts\python.exe download_model.py
.venv\Scripts\python.exe app.py
~~~

Open http://127.0.0.1:5000 and grant camera access. On macOS/Linux, create a virtual environment with python3, activate it, and run the same three Python commands.

## Reproduce training and evaluation

Obtain the original FER-2013 CSV with emotion, pixels and Usage columns; it is not distributed here. Then run:

~~~bash
python -m pip install -r requirements-training.txt
python train.py --csv data/fer2013.csv --epochs 30
python evaluate.py --csv data/fer2013.csv
~~~

The scripts write model metadata and training curves, followed by an evaluation JSON report, per-class metrics and a confusion matrix. Report numbers only after running the scripts on the stated dataset and preserving the outputs and dataset provenance.

## Research context and limitations

Abhishek's NIT Rourkela research involved CNN-based **thermal** facial-expression work and cross-dataset evaluation. **This repository is a separate visible-light webcam project; it does not contain the NIT thermal dataset, model, code or research results.**

A confidence score is not a measure of accuracy or a reliable reading of emotion. Lighting, pose, camera quality and dataset shift can change outputs. Do not use this demo for hiring, healthcare, identity, safety or other consequential decisions.

During local use, frames are processed on the same computer. On a hosted deployment, frames are transmitted to that server for prediction. The app does not save frames or session history to disk, though hosting logs may retain request metadata.

## Checks and team

Run the route and input-handling checks with:

~~~bash
python -m unittest discover -s tests -v
~~~

These checks do not establish model accuracy. Webcam behaviour should also be checked manually on the device running the app.

Developed jointly by [Chitrakant Verma](https://github.com/chitrakantverma) and [Abhishek Yadav](https://github.com/abhi-yadv). The pretrained FER+ model is credited above.
