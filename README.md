# Face Anti-Spoofing Detection

## Task list

- [ ] Create the repository and record environment requirements.
- [ ] Download CelebA-Spoof and inspect images, labels, attack annotations, and usage terms.
- [ ] Implement two versions with/without face detection+cropping.
- [ ] Train a baseline model for genuine vs. spoof classification. (binary model) (time + quality + memory size should be logged)
- [ ] Train a  model for each available attack type. (multiclass model)(time + quality + memory size should be logged)
- [ ] Suggest your datasets/architectures to train. 
- [ ] Build a Streamlit website with image upload and webcam capture.
- [ ] Prepare printed photographs and phone/tablet screen attacks for the live demo.

## Project overview

Face verification systems can encounter attacks such as printed photographs or faces displayed on a screen. We want to train a model that distinguishes a genuine face presentation from a spoofing attempt and demonstrate it through a Streamlit website.

During the presentation, we will show a real face to the webcam, then try printed photographs and faces displayed on a phone or tablet. The website will show the prediction, model score, and processing time. We will also test image uploads.

The main question is how well a model trained on a public dataset can detect attacks captured using our own camera, lighting, and devices.

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


- [CelebA-Spoof dataset repository](https://github.com/ZhangYuanhan-AI/CelebA-Spoof)
- [iBeta biometric testing](https://www.ibeta.com/biometric-testing/)
- [NIST presentation attack detection evaluation](https://pages.nist.gov/frvt/html/frvt_pad.html)
