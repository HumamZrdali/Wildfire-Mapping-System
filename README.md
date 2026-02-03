# Vision-Aided Wildfire Location and Area Mapping System

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-Deep%20Learning-EE4C2C)
![Detectron2](https://img.shields.io/badge/Detectron2-Object%20Detection-black)
![SAM2](https://img.shields.io/badge/SAM2-Segmentation-success)
![Platform](https://img.shields.io/badge/Platform-Google%20Colab-orange)

![System Demo](assets/demo.gif)

## Overview
The **Vision-Aided Wildfire Location and Area Mapping System** is a comprehensive computer vision solution designed to automatically detect, segment, and geolocate wildfires. By leveraging aerial imagery (RGB and Infrared), the system not only identifies the presence of fire and smoke but also calculates the **burned area** and **GPS coordinates** of the fire front.

### Why It Matters
Wildfires spread rapidly and often occur in inaccessible terrain. Traditional detection methods can lack precision regarding the fire's exact spread. This system empowers emergency responders by providing:
* **Urgency Assessment:** Accurate area calculations to gauge fire size.
* **Precision Targeting:** GPS coordinates to guide helicopter pilots for water drops.
* **Spread Analysis:** Segmentation masks that help model how the fire is moving.

---

## Key Features
* **Multi-Spectral Detection:** Specialized models for both **Daytime** (RGB) and **Nighttime** (Infrared/Thermal) scenarios.
* **Advanced Segmentation:** Utilizes **SAM 2 (Segment Anything Model 2)** to convert bounding boxes into precise fire masks.
* **Small Object Detection:** Integrated **SAHI (Slicing Aided Hyper Inference)** to detect small, distant fires by slicing high-resolution images.
* **Geospatial Intelligence:** Automatically calculates the surface area of the fire and converts pixel centroids to real-world GPS coordinates using affine transformations.

---

## Tech Stack & Pipeline

The system employs a multi-stage pipeline to ensure high accuracy:

1.  **Detection (Detectron2):** A Faster R-CNN model trained on custom datasets detects instances of smoke and fire, generating bounding boxes.
2.  **Slicing (SAHI):** Large aerial images are sliced to improve detection accuracy on small fire pockets.
3.  **Segmentation (SAM 2):** Bounding boxes are fed into SAM 2 to generate tight, accurate segmentation masks (isolating fire from background).
4.  **Analysis:**
    * **Area:** Calculated using pixel-to-meter metadata from georeferenced images.
    * **Location:** Mask centroids are mapped to GPS coordinates using Coordinate Reference Systems (CRS).

### Datasets Used
* **Daytime Model:** Trained on **D-Fire** and **FLAME** datasets (~21,000 images).
* **IR Model:** Trained on **Fire Dataset Thermal DJI M30T** and **Infrared Fire CV Model** dataset.
* **Geolocation:** Validated using the **Flame 3** dataset (contains georeferenced metadata).

---

## Performance & Results

Based on the training metrics and evaluation on test sets:

### **Daytime Model**
* **Classification Accuracy:** **96.6%** (Distinguishing fire/smoke from background).
* **Detection Confidence:** The model achieves high confidence, with top detections scoring >92%.
* **Training Stability:** Achieved a low total loss of **0.30**, indicating strong convergence on the training set.

**Daytime Detection & Segmentation**
![Daytime Result](assets/daytime_before_after1.png)
![Daytime Result](assets/daytime_before_after2.png)

### **Infrared (IR) Model**
* **Classification Accuracy:** **95.0%**.
* **Foreground Accuracy:** **90.6%** (Highly effective at correctly identifying fire pixels when present).
* **Precision:** The IR model is exceptionally precise, with many detection confidence scores reaching **99.7%**.

**Infrared Detection & Segmentation**
![IR Result](assets/ir_before_after1.png)
![IR Result](assets/ir_before_after2.png)

**Area Growth & Rate Of Spread Graphs**
The graphs below show the area growth over time and the rate of spread of the fire over time. 
Note: The sudden dips in the graph are caused by frames where the model didn't segment the fire.

![Area Graphs](assets/Area_and_growth_graphs.png)

---

## How to Run

The project is designed to run in a **Google Colab** environment with GPU acceleration.

### Prerequisites
* Google Account (for Colab access).
* GPU Runtime enabled (T4 or better recommended).
* Required Libraries (installed via the notebook): `detectron2`, `sam2`, `sahi`, `torch`, `opencv`.

### Download Model Weights
The trained model weights (`.pth` files) are too large for GitHub. Please download them from the link below and upload them to your Google Colab session:

* **[Download Pre-trained Weights via Google Drive](https://drive.google.com/drive/folders/1l8XdA_6_2WJjWoLl_LX5TYAgMWd16m25?usp=drive_link)**


### Quick Test (Example Data)
Don't have aerial footage handy? We have provided a few sample images in the `examples/` folder of this repository.

1.  Download an image from the [`examples/`](./examples) folder.
2.  Upload it to the Colab notebook when prompted.
3.  See the results immediately!

### Usage Steps
1.  **Open the Notebook:** Upload `Daytime_Code.ipynb` or `IR_Code.ipynb` to Google Colab.
2.  **Upload Assets:** Upload the model weights (`.pth`) you downloaded and your target image/video.
3.  **Environment Setup:** Run the initial cells to install dependencies (Detectron2, SAM2, SAHI) and configure the environment.
4.  **Run Inference:** Execute the main script. The system will:
    * Process the media frame-by-frame.
    * Apply detection and segmentation.
    * Output the processed video/image with overlayed masks and coordinates.

---

## Future Improvements
* **Automated Router:** Implement a classifier to automatically detect time-of-day (Day vs. Night) and load the appropriate model dynamically.
* **Plug-and-Play Interface:** Develop a web interface (Streamlit/Flask) to remove the need for manual Colab setup and code execution.
* **Mask Independence:** Research methods to calculate GPS/Area with reduced dependency on perfect segmentation masks to improve robustness in complex visual conditions.

---

## Project Structure

```text
Wildfire-Mapping-System/
├── assets/                       # Images for the README
│   ├── daytime_before_after1.png 
│   ├── daytime_before_after2.png
│   ├── demo.gif                  
│   ├── ir_before_after1.png
│   ├── ir_before_after2.png      
│   └── Area_and_growth_graphs.png
├── examples/                    # Sample images for testing
│   ├── test_daytime.jpg
│   └── test_ir.jpg
├── metrics/                     # Training logs and JSON metrics
│   ├── Daytime_metrics.json
│   └── IR_metrics.json
├── notebooks/                   # Source code notebooks
│   ├── Daytime_Code.ipynb
│   └── IR_Code.ipynb
├── results/                     # Evaluation outputs (JSONs only)
│   ├── Daytime_coco_instances_results.json
│   └── IR_coco_instances_results.json
├── .gitignore                   # Ignored files (large weights, datasets)
├── README.md                    # Project documentation
└── requirements.txt             # Dependency list
```
---

## References & Datasets

This project was made possible by the following datasets and open-source libraries:

### Datasets
* **D-Fire Dataset:** [Download Link](https://1drv.ms/u/c/c0bd25b6b048b01d/EbLgD7bES4FDvUN37Grxn8QBF5gIBBc7YV2qklF08GCiBw)
* **FLAME Dataset:** [IEEE Dataport](https://ieee-dataport.org/open-access/flame-dataset-aerial-imagery-pile-burn-detection-using-drones-uavs)
* **Fire Dataset Thermal DJI M30T:** [Kaggle](https://www.kaggle.com/datasets/samarthjain247/fire-dataset-thermal-dji-m30t)
* **Infrared Fire Computer Vision Model:** [Roboflow](https://universe.roboflow.com/hanyang-university-fgjqu/infrared-fire/dataset/4/download)

### Key Libraries & Papers
* **Detectron2:** Wu, Y., et al. "Detectron2" ([https://github.com/facebookresearch/detectron2](https://github.com/facebookresearch/detectron2)).
* **SAM 2:** Ravi, N., et al. "Segment Anything Model 2" ([https://github.com/facebookresearch/segment-anything](https://github.com/facebookresearch/segment-anything)).
* **SAHI:** Akyon, F. C., et al. "Slicing Aided Hyper Inference" ([https://github.com/obss/sahi](https://github.com/obss/sahi)).

---

## Citation

If you find this project useful for your research, please cite it as follows:

```bibtex
@software{VisionAidedWildfire2026,
  author = {Mhd Humam ZRDALI},
  title = {Vision-Aided Wildfire Location and Area Mapping System},
  year = {2026},
  url = {https://github.com/HumamZrdali/Wildfire-Mapping-System}
}