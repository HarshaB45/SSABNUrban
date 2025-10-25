# SSABNUrban

This project implements the Spatial-Spectral Attention Bilateral Network (SSABN) for hyperspectral unmixing, based on the paper: 

*Zhiru Yang et al., "Spatial-Spectral Attention Bilateral Network for Hyperspectral Unmixing," IEEE GRSL, 2023.*

Environment Used - Google Colab

# About Hyperspectral Unmixing
Hyperspectral Unmixing is a process that decomposes mixed pixels into pure pixels and estimates their abundance within that pixel. 

**Mixed Pixels** are a result of the resolution of sensors. Many pixels capture light from multiple materials, therefore one pixel 
can constitute multiple materials

**Endmembers** are unique spectral signatures of pure materials present in the image. 

**Abundances** are the amount of endmembers present in the image.

The mixing model used in this paper was the **Linear Mixing Model**, which can be represented as 

*X = EA + N*

X - Hyperspectral Image

E - Endmember Matrix

A - Abundance Matrix 

N - Noise Matrix

where noise can be defined as variations in the true spectral values in the image.

**Blind Hyperspectral Unmixing** - You do not need the endmembers, you directly estimate them from the image.
The abundances and endmembers of each pixel are estimated from the image.

# Urban Dataset

![Diagram](assets/urbanimage.png)

Urban dataset has a hyperspectral image with dimensions 307 × 307 pixels and 210 spectral bands ranging from 400 nm to 2500 nm, after removing noisy and water absorption bands. 

The image contains 4 endmembers; Asphalt Road, Roof, Grass, Tree. These endmembers are used in spectral unmixing to identify and estimate the proportion of each material within every pixel, since most pixels contain mixtures of multiple materials due to the high spatial complexity of urban areas.

# Architecture

![Diagram](assets/architecture.png)


### **1. Input and Unfolding**
The network takes a **hyperspectral image (HSI)** as input — a 3D cube with spatial and spectral dimensions.  
The cube is **unfolded** into pixel-wise spectral vectors \( X \), allowing each pixel’s spectral information to be processed individually while retaining overall spatial context.

---

### **2. Encoder**
The **encoder** extracts both spectral and spatial features through two main branches:

#### 🔹 Spectral Branch
- **Conv(1×1):** Projects each spectral vector into a latent feature space.  
- **LeakyReLU:** Adds nonlinearity for complex feature learning.  
- **Dropout:** Reduces overfitting by randomly dropping connections.  
- **SCAM (Spectral–Channel Attention Module):** Learns attention across spectral channels, emphasizing important wavelength relationships.  
- Repeated **Conv–LeakyReLU–Dropout–SCAM** layers capture hierarchical spectral dependencies.

#### 🔹 Spatial Branch
- **Conv(3×3):** Extracts local spatial context from the HSI cube.  
- **LeakyReLU + Dropout:** Similar activation and regularization steps.  
- **STB (Swin Transformer Block):** Uses **window-based multi-head self-attention (W-MSA and SW-MSA)** to model long-range spatial dependencies while maintaining computational efficiency.

The spectral and spatial features are fused to form a **joint latent representation** of the HSI.

---

### **3. Decoder**
The **decoder** reconstructs the hyperspectral image from the encoded representation:

- **Conv(1×1):** Refines the fused features into abundance estimates.  
- **ASC + ANC Constraints:**  
  - **ASC (Abundance Sum-to-One Constraint):** Ensures all abundances per pixel sum to one.  
  - **ANC (Abundance Non-Negativity Constraint):** Enforces non-negative abundance values.  
- **Linear Decoder:** Combines the estimated abundance maps and learned endmembers to reconstruct the HSI.

---

### **4. Outputs**
- **Abundance Maps:** Show the spatial distribution of each material/component.  
- **Endmembers:** Represent the pure spectral signatures learned by the model.  
- **Reconstructed HSI:** The final output, approximating the original hyperspectral image.

---



Presentation - https://docs.google.com/presentation/d/11CiYy9N1HPy-sw6BZI3bKO7vEWBu1FAo/edit?slide=id.p12#slide=id.p12
# Prerequisites
```
python >= 3.8
torch == 2.5.1
torchvision == 0.20.1
numpy == 1.24.0
scipy == 1.14.1
matplotlib == 3.6.3
```
# Installation
cd project-folder
pip install torch==2.5.1 torchvision==0.20.1 numpy==1.24.0 scipy==1.14.1
matplotlib==3.6.3

# How to Run
# In order to run this project on Colab:

- **Step 1:** Open the file titled "SSABNUrban.ipynb".
- **Step 2:** Upload the datasets UrbanR162.mat and groundTruth_4_end.zip one of two ways:
  - Run the first cell, and upload the files using the **Choose Files** button
  - Upload the files unto Colab runtime
- **Step 3:** Run all cells 






