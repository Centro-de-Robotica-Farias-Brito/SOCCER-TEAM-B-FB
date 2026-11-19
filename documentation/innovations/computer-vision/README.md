# Innovation 1 — Efficient Computer Vision Algorithm for Edge Detection

## 1. Problem and Proposed Solution

One of the main challenges in developing our autonomous soccer robot was creating a vision system capable of detecting the ball accurately and quickly, even under varying lighting conditions and image noise, while respecting the processing and memory limitations of the onboard camera.

After reviewing Technical Documentation Papers (TDPs) from teams that participated in previous competitions, we found approaches based on color thresholds, contours, blobs, and neural networks. However, we did not identify the same combination of operations used in our project.

We therefore developed a custom edge detection algorithm based on **local luminance and chromatic discontinuities**, designed to identify relevant transitions without relying on the grouping of pixels into connected regions.

## 2. Algorithm Design and Technical Contribution

Our method is based on the principle that an edge can be identified through an abrupt change in the continuity of luminance or chromatic information.

The algorithm follows these steps:

1. **Squared chroma calculation:** The squared chroma of each pixel is calculated.
2. **Local neighborhood analysis:** The eight neighboring pixels are examined.
3. **Adaptive threshold:** A local threshold is determined from the minimum and maximum chroma values within the neighborhood.
4. **Transition direction estimation:** The distribution of neighboring pixels above and below the threshold is used to estimate the local direction of the luminance transition.
5. **Directional pixel comparison:** This direction determines which neighboring pixel is compared with the central pixel.
6. **Edge detection and hue weighting:** When a significant chromatic difference is detected, the corresponding edge is highlighted. The result is weighted by hue, emphasizing color transitions relevant to ball detection.

The main technical contribution lies in combining chromatic analysis, local adaptive thresholding, directional pixel selection, and hue weighting.

This approach enables direct edge detection without requiring blob formation or neural network inference.

## 3. Computational Optimization

To reduce processing time and memory usage, we implemented the algorithm using:

- Integer arithmetic to avoid unnecessary floating-point operations.
- Precomputed lookup tables to reduce repeated calculations.
- A circular buffer that stores only the image rows required for local processing.
- A single-pass image-processing strategy.

### Time Complexity

For an image of dimensions N × N, the algorithm has a time complexity of **O(N²)**.

Each pixel is processed once, with a constant number of operations per pixel.

This is asymptotically optimal for an algorithm that must examine every pixel in the image.

### Auxiliary Memory Complexity

The algorithm requires **O(N) auxiliary memory**.

The circular buffer reuses allocated storage and avoids maintaining additional full-frame image copies.

This approach is particularly useful for embedded vision systems with limited available memory.

## 4. Integration with Autonomous Robot Behavior

The algorithm is integrated into the robot's omnidirectional vision system, providing information for ball identification and tracking.

The resulting observations contribute to the autonomous motion-planning system, which uses a Kalman filter to estimate the ball's trajectory.

These estimates support decisions involving:

- Direct ball pursuit.
- Predicted interception.
- Elliptical encirclement.

The edge detection algorithm therefore contributes to a broader perception and decision-making architecture rather than operating as an isolated image-processing component.

## 5. Testing and Preliminary Results

Internal tests conducted during development showed promising results regarding:

- Detection accuracy.
- Resistance to image noise.
- Computational efficiency.

These initial observations suggest that the method is suitable for further evaluation in real-time embedded vision applications.

## 6. Innovation and Contribution

Our contribution is the combination of chromatic and directional operations within a local image-processing algorithm designed for autonomous robotic vision.

The method aims to provide relevant edge information with bounded computational cost and low auxiliary memory requirements.

Rather than claiming that individual image-processing techniques are new, our work focuses on their particular combination and optimization for the robot's embedded vision system.

## 7. Applicability to Other Teams

The approach can be reproduced, evaluated, and adapted by teams interested in efficient edge detection on resource-constrained embedded devices.

Its main transferable principles include:

- Local chromatic analysis.
- Direction-aware edge detection.
- Fixed-size buffering.
- Integer-based processing.
- Memory-efficient image scanning.

These design choices may also be useful in other real-time robotic vision applications.
