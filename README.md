# PCS 2025 Supplymentary Material

## LiteVPNet: A Lightweight Network for Video Encoding Control in Quality-Critical Applications

Author(s): Vibhoothi Vibhoothi,François Pitié, Anil Kokaram
Contact:  vibhootv at tcd dot ie

## Table of Contents
- [PCS 2025 Supplymentary Material](#pcs-2025-supplymentary-material)
  - [LiteVPNet: A Lightweight Network for Video Encoding Control in Quality-Critical Applications](#litevpnet-a-lightweight-network-for-video-encoding-control-in-quality-critical-applications)
  - [Table of Contents](#table-of-contents)
  - [Abstract](#abstract)
  - [Datasets](#datasets)
    - [Runtime Complexity Evaluation Dataset](#runtime-complexity-evaluation-dataset)
  - [Feature Information](#feature-information)
    - [Feature Vector Composition](#feature-vector-composition)
    - [AV1 Bitstream Symbol Categories](#av1-bitstream-symbol-categories)
  - [Conclusion](#conclusion)



## Abstract
In the last decade, video workflows in the cinema production ecosystem have presented new use cases for video streaming technology. These new workflows e.g. in On-set Virtual Production, present the challenge of requiring precise quality control and energy efficiency. Existing approaches to transcoding often fall short of these requirements, either due to a lack of quality control or computational overhead. To fill this gap, we present a lightweight neural network (LiteVPNet) for accurately predicting Quantisation Parameters for NVENC AV1 encoders that achieve a specified VMAF score. We use low-complexity features including bitstream characteristics, video complexity measures, and CLIP-based semantic embeddings. Our results demonstrate that LiteVPNet achieves mean VMAF errors below 1.2 points across a wide range of quality targets. Notably, LiteVPNet achieves VMAF errors within 2 points for over 87% of our test corpus, c.f. ≈61% with state-of-the-art methods. LiteVPNet’s performance across various quality regions high- lights its applicability for enhancing high-value content transport and streaming for more energy-efficient, high-quality media experiences.


## Datasets

A large and diverse dataset was compiled to train and validate the `LiteVPNet` model for QP (Quantisation Parameter) prediction.

*   **Size**: A total of **2944 videos**.
*   **Characteristics**:
    *   All videos are single-shot without scene changes.
    *   Video duration is typically under 7 seconds, with an average frame count of 300 frames.
    *   Content includes various frame rates and 1080p resolutions (e.g., 1920x1080, 1080x1920, 1080x1080).
*   **Sources**: The videos were sourced from a wide range of public and industry-standard datasets to ensure diversity and robustness:
    *   YouTube UGC dataset
    *   Netflix Open-content
    *   AOM-CTC (Alliance for Open Media - Common Test Conditions)
    *   Xiph.org Media set
    *   Yonsei Dataset
    *   Bristol Video Texture Database
    *   YouTube SFV dataset
    *   American Society of Cinematographers (ASC) SteM2
    *   Ultravideo group dataset
    *   SJTU Dataset
    *   Sony Cine Test Footages
    *   Inter4K dataset

### Runtime Complexity Evaluation Dataset

This dataset used for runtime analysis of `LiteVPNet` against other state-of-the-art models.

*   **Content**: **139 video shots** extracted from two short films from the Netflix Open-Content library:
    1.  *Meridian* (12 minutes)
    2.  *NocturneRoom* (11 minutes)
*   **Processing**: The original mezzanine files (MXF using JPEG2000 in HDR) were converted to SDR using Dolby Vision Trim-Pass Metadata via DaVinci Resolve. Scene detection was performed using `Pyscenedetect`.

## Feature Information

The `LiteVPNet` model uses a comprehensive feature vector of dimension **754x1** derived from multiple sources: bitstream analysis, Video Complexity Analyzer (VCA) metrics, video metadata, and OpenAI CLIP embeddings.

### Feature Vector Composition

The following table details the components of the feature vector. Features are extracted at both the frame level (from the first 8 frames) and the overall video level.

| #  | Name                                               | Measurement Level | Count | Total Count |
|----|----------------------------------------------------|-------------------|-------|-------------|
| 1  | Total bits (All, MV Blocks, Non-MV Blocks)         | Frames            | 3     | 24          |
| 2  | TransformType percentage                           | Frames            | 16    | 128         |
| 3  | Compound mode (Avg, Wedge, Diffwtd)                | Frames            | 3     | 24          |
| 4  | IntraBC Percentage (1/0)                           | Frames            | 2     | 16          |
| 5  | Palette modes (8 colours)                          | Frames            | 8     | 64          |
| 6  | UV Palette modes (8 colours)                       | Frames            | 8     | 64          |
| 7  | Skip Mode Percentage (1/0)                         | Frames            | 2     | 16          |
| 8  | Per-Component Distribution (Symbols)               | Frames            | 20    | 160         |
| 9  | BlockSize distribution                             | Video             | 22    | 22          |
| 10 | Transform Size                                     | Video             | 19    | 19          |
| 11 | Transform Type                                     | Video             | 16    | 16          |
| 12 | UV Inter Prediction Mode                           | Video             | 15    | 15          |
| 13 | Inter Pred mode Types                              | Video             | 26    | 26          |
| 14 | Motion Pred Types (Translational, OBMC, Wrapped)   | Video             | 3     | 3           |
| 15 | Compound mode (Avg, Wedge, Diffwtd)                | Video             | 3     | 3           |
| 16 | IntraBC Percentage (1/0)                           | Video             | 2     | 2           |
| 17 | Palette modes (8 colours)                          | Video             | 8     | 8           |
| 18 | UV Palette modes (8 colours)                       | Video             | 8     | 8           |
| 19 | Reference Frame Type                               | Video             | 9     | 9           |
| 20 | Inter-Pred Filter (Dual-Filter)                    | Video             | 9     | 9           |
| 21 | Skip Mode Percentage (1/0)                         | Video             | 2     | 2           |
| 22 | Per-Component Distribution (Symbols)               | Video             | 25    | 25          |
| 23 | MV Stats (mean, min, max, stddev, kurtosis, skew)  | Video             | 6     | 6           |
| 24 | All-Frames VCA {mean, min, max, stddev, 25%, 50%, 75%} | Video             | 21    | 21          |
| 25 | I-Frame VCA {mean, min, max, stddev, 25%, 50%, 75%}    | Video             | 21    | 21          |
| 26 | Non-I Frame VCA {mean, min, max, stddev, 25%, 50%, 75%}| Video             | 21    | 21          |
| 27 | Video Metadata (bitrate\_kbps, duration, etc.)     | Video             | 6     | 6           |
| 28 | Clippie Embedding                                  | Video             | 16    | 16          |
|    |                                                    | **Total Length**  |       | **754**     |

Feature is extracted using `inspect` tool of libaom-av1. 

Sample CLI
> `inspect $INPUT.ivf  --all -x > $INPUT-stats.json`

### AV1 Bitstream Symbol Categories

For bitstream feature analysis, AV1 symbols are grouped into functional categories to analyze bit allocation and computational complexity. The following table lists these categories and their corresponding symbol names.

| Category                           | Symbol Names                                                                                                                                                                                                                                                                            |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Block Structure and Partitioning** | `read_partition`, `read_delta_qindex`                                                                                                                                                                                                                                                   |
| **Prediction Mode Symbols**        | `read_intra_mode`, `read_intra_mode_uv`, `read_filter_intra_mode_info`, `read_angle_delta`, `read_skip_txfm`                                                                                                                                                                                |
| **Transform and Coefficient Processing** | `read_selected_tx_size`, `av1_read_tx_type`, `read_coeffs_txb`, `read_coeffs_reverse_2d`, `read_coeffs_reverse`                                                                                                                                                                        |
| **Restoration and Filtering**      | `loop_restoration_read_sb_coeffs`, `read_sgrproj_filter`, `read_wiener_filter`, `read_cdef`                                                                                                                                                                                               |
| **Palette Mode Symbols**           | `read_palette_mode_info`, `read_palette_colors_y`, `read_palette_colors_uv`, `decode_color_map_tokens`                                                                                                                                                                                     |
| **Chroma from Luma (CfL) Processing** | `cfl:signs`, `cfl:alpha_u`, `cfl:alpha_v`                                                                                                                                                                                                                                               |
| **Entropy Coding Helpers**         | `av1_read_uniform`, `read_golomb`                                                                                                                                                                                                                                                           |



## Conclusion

This work introduced LiteVPNet to predict QP parameters for quality-constrained encoding with AV1 in cinema production.
The system is an efficient neural network that combines diverse feature sets, including bitstream statistics, VCA, and semantic CLIP embeddings, processed via a self-attention-based model. It achieves state-of-the-art performance, demonstrated by a mean QP MAE of 4.5 and a mean VMAF MAE of 1.0. Furthermore, our results in a sense highlight the non-linearity inherent in R/D optimisation: moderate QP variations (errors of 3.9-5.1) yield considerably smaller VMAF errors (0.8-1.2), indicating precise perceptual control. Future work will include dynamic resolution and support for HDR videos.