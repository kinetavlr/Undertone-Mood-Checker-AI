# Undertone & Mood Checker + Makeup Recommender

> A hybrid AI system that detects a user's skin undertone and mood from their face, then recommends real makeup products (cushion, lipstick, blush) that match. Built for a university AI course project (PKM-AI), aligned with **SDG 12 — Responsible Consumption and Production**.

## Background

A lot of people struggle to identify their own skin undertone, which leads to buying makeup shades that don't actually suit them — and eventually, wasted products. We built this tool to take the guesswork out of it: show your face, and the system tells you your undertone (warm / cool / neutral) and skin depth, reads your mood, and recommends real products from actual beauty brands that fit both.

## How It Works

The pipeline combines computer vision, two custom-trained CNNs, and a rule-based heuristic layer:

1. **Face detection** — MediaPipe (pretrained CNN) detects the face and extracts the relevant patch (cheek/forehead area).
2. **Undertone classification** — a custom CNN (ResNet18, fine-tuned) classifies the skin patch into **warm / cool / neutral**.
3. **Skin depth estimation** — a heuristic algorithm using the **LAB color space** (chosen over YCbCr for being closer to human visual perception and more stable across lighting) estimates light/medium/dark depth from luminance.
4. **Mood/expression detection** — a second, separately trained CNN classifies facial expression (trained on FER2013), collapsed into higher-level mood groups (`happy`, `neutral`, `sad`, `angry` → mapped to a **bright** or **bold** makeup style).
5. **Recommendation engine** — a rule-based matching engine looks up a JSON product database and returns a full look: **Cushion** (strict match on undertone + skin depth, since base color accuracy matters most), **Lipstick**, and **Blush** (matched on undertone + mood).
6. **Interface** — packaged as a web app with Gradio, supporting both live webcam and image upload.

## Tech Stack

| Component | Tool/Library |
|---|---|
| Face detection & landmarks | MediaPipe |
| Undertone model | PyTorch, ResNet18 (custom-trained) |
| Mood model | Custom CNN, trained on FER2013 |
| Color analysis | LAB color space, CLAHE preprocessing |
| Recommendation logic | Rule-based engine, JSON product database |
| Interface | Gradio |
| Training environment | Google Colab |

## Dataset

- **Undertone:** 200 face images, manually collected and labeled (no public undertone dataset exists) — 155 for training / 45 for validation, roughly balanced across warm/cool/neutral. Preprocessed with resizing (224×224), normalization, and augmentation (±15° rotation, horizontal flip).
- **Mood:** [FER2013](https://www.kaggle.com/datasets/msambare/fer2013) (Kaggle) — 28,709 training / 3,589 test images, 48×48 grayscale, 7 base emotions collapsed into higher-level mood groups for cleaner integration with the recommendation engine.

## Results

| Model | Metric | Result |
|---|---|---|
| Undertone classifier (ResNet18) | Validation accuracy | **82.22%** |
| Mood classifier (CNN, FER2013) | Test accuracy | **63.88%** |

Despite a fairly small, manually-labeled undertone dataset, the model generalized well. The main limitations we found: lighting conditions and makeup on the subject's face can shift the predicted undertone, and a small/limited dataset makes the model easier to "trick" with extreme lighting or background color — something we flagged as an area for future work (better augmentation + lighting normalization).

## My Contribution

I worked on the **mood dataset** — collecting and labeling the FER2013 subset used for training — and contributed to the conceptual design of the system's workflow and feature set (I also co-wrote the Abstract and Introduction sections of our paper). It was my first time working hands-on with an image dataset at this scale, and it gave me a much better feel for how much data quality (not just quantity) affects a CNN's ability to generalize.

## Potential Applications

- Beauty retailers/e-commerce — reduce "wrong shade" purchases and product waste
- Makeup artists (MUA) — quickly and objectively confirm a client's undertone
- Personal use — a quick, no-guesswork way to shop for makeup online

## Team

Group project (PKM-AI), Cyber Security program — BINUS University, supervised by our course lecturer.

---
*Built for an academic AI course project. Trained and demoed in Google Colab / Gradio.*
