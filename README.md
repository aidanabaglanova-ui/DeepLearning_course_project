# Face Anti-Spoofing Detection

## Task list

- [ ] Create the repository and record environment requirements.
- [ ] Download CelebA-Spoof and inspect images, labels, attack annotations, and usage terms.
- [ ] Prepare training, validation, and test data without identity leakage.
- [ ] Define which attack categories can be supported by the available annotations.
- [ ] Implement face detection, cropping, resizing, and data loading.
- [ ] Train a baseline model for genuine vs. spoof classification.
- [ ] Compare binary cross-entropy with focal loss and test data augmentation.
- [ ] Evaluate performance overall and separately for each available attack type.
- [ ] Save the selected checkpoint and create a reusable inference function.
- [ ] Build a Streamlit website with image upload and webcam capture.
- [ ] Prepare printed photographs and phone/tablet screen attacks for the live demo.
- [ ] Test the complete application, document failures, and prepare the final report and poster.

## Project overview

Face verification systems can encounter attacks such as printed photographs or faces displayed on a screen. We want to train a model that distinguishes a genuine face presentation from a spoofing attempt and demonstrate it through a Streamlit website.

During the presentation, we will show a real face to the webcam, then try printed photographs and faces displayed on a phone or tablet. The website will show the prediction, model score, and processing time. We will also test image uploads.

The main question is how well a model trained on a public dataset can detect attacks captured using our own camera, lighting, and devices.

**Status:** planned course project. The tasks and experiments below describe intended work, not completed results.

## Classes and attack types

The first model will use two output classes: **genuine** and **spoof**. We will also inspect attack-type annotations so that we can evaluate which attacks are easier or harder to detect.

| Category to investigate | Example | Planned use |
| --- | --- | --- |
| Genuine / bona fide | A real person in front of the camera | Training, evaluation, and live demo |
| Print attack | A face printed on paper and shown to the webcam | Main attack for the live demo |
| Display attack: still image | A face photograph shown on a phone, tablet, or monitor | Main attack for the live demo |
| Display attack: video replay | A prerecorded face video played on a screen | Additional demo if feasible |
| Other annotated attacks | Other types identified during dataset inspection, such as masks or cutouts if available | Optional evaluation depending on labels and sample counts |

These are our planned evaluation groups, not a confirmed list of CelebA-Spoof labels. We will check the dataset annotations before deciding the final mapping. Still-image displays and video replays may need to be grouped together if the labels do not distinguish them.

An optional extension is a multiclass model that predicts **genuine, print, or display**. We will implement this only if suitable labels and enough examples are available. A binary model cannot identify the attack type, so the website will not display a predicted subtype unless a subtype classifier has been trained and evaluated.

This project focuses on physical presentation attacks. Detecting digitally generated deepfakes would require separate data and evaluation.

## Dataset and preparation

We plan to use CelebA-Spoof as the primary dataset. Before training, we will inspect its label format, attack categories, official split, license, and permitted use.

- Follow the official evaluation split and keep the test data untouched during development.
- Create a validation split from training data with separate identities where identifiers are available.
- Record class counts and attack-type counts in each split.
- Detect and crop faces with a consistent margin, retaining some surrounding context.
- Use the same input size and normalization for training and inference.
- Apply moderate training-only augmentation, such as brightness changes and small rotations, and check that it preserves useful spoof cues.
- Keep our own camera samples separate from training to assess performance on new capture conditions.

## Model and experiments

We will fine-tune a pretrained image classifier for genuine/spoof detection. The initial backbone will be selected based on available GPU memory and inference speed.

| Experiment | Purpose |
| --- | --- |
| Baseline with binary cross-entropy | Establish initial performance |
| Class-weighted binary cross-entropy, if needed | Check a simple approach to class imbalance |
| Focal loss | Test whether focusing on difficult examples improves detection |
| Selected loss with augmentation | Test robustness to capture conditions |
| Optional attack-type classification | Explore genuine/print/display predictions |

We will compare experiments using the same data splits and record configurations and random seeds. Focal loss is a hypothesis to test, not an assumed improvement. The decision threshold will be selected using validation data and fixed before final testing.

## Streamlit website

The application will provide:

- An image upload option.
- A webcam capture option to take a new frame for each attempt.
- A preview of the submitted image and detected face.
- A genuine/spoof prediction and a clearly labeled spoof score.
- Processing time for each prediction.
- A request to try again if no face or multiple faces are detected.
- A short project description and a page showing evaluation results.

The first version will use webcam snapshots. Continuous webcam streaming is an optional extension and would require a streaming component. Repeated snapshots are sufficient for the planned live demonstration.

The app will call a shared inference function directly. A separate API is optional. The inference function will load the checkpoint, preprocess the input, run the model, and return the score and prediction using the selected threshold. Model scores will not be presented as guaranteed probabilities of authenticity.

## Live demonstration

1. Open the Streamlit application and explain the genuine/spoof task.
2. Capture a real face and show its prediction and score.
3. Hold a printed face photograph in front of the same webcam and capture it.
4. Display a face photograph on a phone or tablet and repeat the test.
5. If feasible, play a prerecorded face video on the screen and capture frames.
6. Repeat selected attempts with different distances, angles, and lighting.
7. Discuss successful detections and failures alongside the held-out evaluation results.

Prepare consent-based face photographs, printed copies, a phone or tablet, a webcam, and a working local installation. Keep a few consented example images as a backup if camera access fails. Record the true presentation type separately from the model prediction. Demo examples will not be used to tune the model or threshold if they are reported as test results.

## Evaluation

We will report accuracy, precision, recall, F1-score, ROC-AUC, and a confusion matrix. We will also report the proportion of attacks accepted as genuine and genuine presentations rejected as attacks, overall and by attack type where possible.

Evaluation will cover:

- Held-out dataset performance.
- Performance on our own camera and attack samples, reported separately.
- Differences between print and display attacks.
- Sensitivity to lighting, distance, and device changes.
- Inference time and full application response time.

If we implement multiclass classification, we will additionally report macro F1 and per-class recall. We will record sample counts and avoid treating multiple frames from one capture as independent attack attempts. Numerical targets will be set after the baseline is measured.

## Five-week project plan

| Week | Work | Milestone |
| --- | --- | --- |
| 1 | Review related work, set up the repository, access the dataset, inspect labels and attack types | Agreed scope and dataset inventory |
| 2 | Prepare data, implement preprocessing, train the baseline, and create inference code | Working checkpoint and documented inference function |
| 3 | Run focal-loss and augmentation experiments; build the Streamlit interface in parallel | Initial results and upload-based application |
| 4 | Select the model and threshold, integrate webcam capture, and prepare physical attacks | Complete interim demo |
| 5 | Run final testing, analyze failures, rehearse the demonstration, and finish documentation | Final website, code, report, and poster |

After the baseline is ready, model improvement, evaluation preparation, and website development can proceed in parallel. The handover should include the checkpoint, preprocessing settings, label mapping, threshold, example input, and instructions for running inference.

## Resources and reproducibility

Planned compute: one CUDA-capable GPU for training. We will measure whether CPU inference is sufficient for the demo.

Planned tools: Python, PyTorch/torchvision, OpenCV, scikit-learn, and Streamlit. The repository will include pinned dependencies, training and evaluation configurations, seeds, split manifests, checkpoint instructions, and application run instructions once implemented. Dataset files and personal demo images will not be committed to the public repository.

## Risks and ethics

Class imbalance will be addressed through loss comparisons and per-class evaluation. Identity leakage will be reduced through separate splits, and overfitting will be monitored on validation data. Testing on our own devices will help reveal differences between the training data and live conditions.

We will use personal demo images only with consent, avoid storing webcam captures by default, and agree on deletion of collected samples after the project. The application is a course prototype, and its limitations and failed detections will be reported.

## Testing references

We plan to review these resources when designing our evaluation. The project does not claim independent certification or participation in these evaluations.

- [CelebA-Spoof dataset repository](https://github.com/ZhangYuanhan-AI/CelebA-Spoof)
- [iBeta biometric testing](https://www.ibeta.com/biometric-testing/)
- [NIST presentation attack detection evaluation](https://pages.nist.gov/frvt/html/frvt_pad.html)

## Final deliverables

- Trained model checkpoint and inference function.
- Reproducible training and evaluation code.
- Streamlit application with upload and webcam capture.
- Experiment comparison and attack-type analysis.
- Live demonstration with genuine, print, and display examples.
- Final report, poster, and setup instructions.
