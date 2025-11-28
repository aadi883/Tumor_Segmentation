Semi-Supervised Segmentation of WSI via Consistency Regularization

📌 Project Overview

This project implements a Semi-Supervised Learning (SSL) pipeline for the semantic segmentation of Whole Slide Images (WSI). Addressing the scarcity of pixel-level annotations in computational pathology, this framework leverages a Teacher-Student architecture with Consistency Regularization (inspired by FixMatch) to learn from massive amounts of unlabeled data.

The core objective was to evaluate the efficacy of SSL when applied to histopathology data and to stress-test the algorithm's robustness against biological domain shift.

🔬 Key Features

WSI Preprocessing: Automated patching of gigapixel .ndpi slides using OpenSlide.

Tissue Detection: HSV-based color filtering to discard background glass and retain only biological tissue.

Architecture: U-Net backbone with a ResNet-style encoder path.

SSL Strategy:

Weak Augmentation (Teacher): Flips/Rotations to generate Pseudo-Labels.

Strong Augmentation (Student): Gaussian Blur/Noise to force consistency.

Confidence Thresholding: Only high-confidence predictions ($p > 0.70$) contribute to the loss.

🧪 Experimental Design & "Negative Transfer" Study

A critical component of this project was investigating the impact of Different Data Shift on Semi-Supervised Learning.

Dataset Configuration

Labeled Data (Source): Breast tumor tissue from Kaggle.

Unlabeled Data (Target): Camelyon16/17 (Breast/Lymph Node) tissue from Whole Slide Images.

Hypothesis

Standard SSL theory suggests that adding unlabeled data improves generalization. However, in medical imaging, we hypothesized that if the histological phenotype (tissue type) differs significantly between labeled and unlabeled sets, the model might suffer from Negative Transfer.

Results

Experiment Stage

Dice Score

Observation

Stage 1: Supervised Baseline

0.8834

Trained only on labeled kaggle data.

Stage 2: SSL Fine-Tuning

0.8600

Fine-tuned with unlabeled Breast/Lymph patches.

📉 Analysis: The "Negative Transfer" Phenomenon

The slight decrease in performance ($-0.0234$) confirms the sensitivity of SSL to data alignment alignment.

Teacher Confusion: The Teacher model, trained on brain tissue, generated noisy pseudo-labels when processing breast tissue structures (e.g., misclassifying milk ducts as tumor features).

Student Corruption: The Student model learned these noisy correlations, slightly degrading its ability to segment the original breast tissue test set.

Conclusion: "Unlabeled data is not free." For SSL to succeed in pathology, strict histological alignment between the labeled and unlabeled distributions is required. This experiment serves as a baseline for future work using matched TCGA-GBM data.

🛠️ Technology Stack

Deep Learning: PyTorch, Torchvision

Architecture: U-Net

Augmentations: Albumentations (Spatial & Pixel-level)

WSI Handling: OpenSlide, Pillow, OpenCV

Visualization: Matplotlib

🚀 Usage

1. Prerequisites

# Install PyTorch and Dependencies
pip install torch torchvision albumentations opencv-python-headless
# Install OpenSlide (Linux/Colab)
apt-get install openslide-tools
pip install openslide-python


2. Pipeline Execution

The pipeline is designed to run in three sequential phases:

Phase 0: Ingestion
Downloads raw WSI files and extracts $224 \times 224$ tissue patches.

# (Logic contained in notebook)
extract_patches(slide_path, DEST_DIR)


Phase 1: Baseline Training
Trains the U-Net on the limited labeled dataset using Supervised Loss (Dice + BCE).

# Training Loop
train_epoch(model, optimizer, epoch, is_ssl=False)


Phase 2: SSL Fine-Tuning
Loads the Teacher weights and resumes training using the Unlabeled stream and Consistency Loss.

# SSL Loop
train_epoch(model, optimizer, epoch, is_ssl=True)


📊 Visualizations

The model outputs segmentation masks comparing:

Original Image

Ground Truth Mask

Predicted Segmentation



👤 Author

Aaditya Rao: Experimenting with the boundaries of Semi-Supervised Learning in Medical AI.
