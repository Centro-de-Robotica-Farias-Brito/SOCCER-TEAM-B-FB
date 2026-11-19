# Innovation 2 — Hierarchical Eisenstein Spatial Representation Integrated with Autonomous Control

## 1. Problem and Objective

One of the main challenges in developing our attacking robot was creating a system capable of locating, tracking, and intercepting the ball accurately while maintaining low memory consumption and predictable processing time on the ESP32-S3.

Conventional Cartesian coordinates (x, y) allow objects to be located in a two-dimensional space, but they do not inherently provide a hierarchical organization of spatial information.

Our objective was therefore to develop a solution that would not only represent positions compactly but also participate directly in the geometric calculations and motion-planning decisions of the robot.

## 2. Proposed Solution and Technical Contribution

We developed an **implicit hierarchical spatial structure**, inspired by the successive refinement concept of segment trees and based on complex Eisenstein numbers.

The representation uses the base:

**β = 1 − ω**

where ω is a complex cube root of unity.

In our implementation, each field position is represented by a path containing eight 3-bit digits, totaling **24 bits (3 bytes)**.

Each digit corresponds to a successive level of spatial refinement, allowing positions to be represented on a hexagonal lattice without explicitly storing the nodes of a tree.

### Compact Spatial Representation

A conventional Cartesian position stored using two 32-bit floating-point values requires 64 bits (8 bytes).

Our representation uses only 24 bits per position, providing a **62.5% reduction in per-position storage**.

This reduction applies specifically to the coordinate representation. Total system memory consumption also depends on lookup tables and auxiliary data structures.

### Spatial Resolution and Quantization

The selected configuration uses one integer digit and seven fractional digits.

It provides:

| Property | Value |
|---|---|
| Hierarchical depth | 8 digits |
| Bits per digit | 3 bits |
| Storage per position | 24 bits (3 bytes) |
| Reduction vs. two float32 values | 62.5% |
| Approximate spacing between neighboring points | 1.689 cm |
| Maximum theoretical quantization error | 0.975 cm |

The quantization error represents the theoretical discretization limit of the mathematical representation.

It does not account for physical sensor errors, measurement uncertainty, or real-world localization accuracy.

## 3. Canonical Encoding

The adopted Eisenstein representation contains redundancy: different digit sequences may correspond to the same spatial position.

To address this issue, we developed a canonical encoder that selects a consistent representation for each position.

This is important because equivalent positions should not produce inconsistent internal encodings during geometric processing and motion planning.

### Initial Approach: Backtracking

The first version of our encoder used backtracking to search for valid digit paths.

Although this approach could find valid representations, its computational cost could vary depending on the input.

Such variability is undesirable in a robotic controller that must make decisions continuously.

### Optimized Approach: Bounded-State Encoding

We redesigned the algorithm to maintain a bounded number of intermediate candidate states at each hierarchical level.

This eliminated the need for recursive backtracking and provided more predictable execution.

Under fixed-precision arithmetic and a constant bound on the number of states per level, the algorithm has a time complexity of:

**O(D)**

where D represents the number of encoding digits.

Additional implementation optimizations include:

- Compact state representations.
- Direct bit manipulation.
- Fixed-size buffers.
- Precomputed lookup tables.
- Avoidance of dynamic memory allocation during encoding.

These optimizations were designed to make the representation practical for resource-constrained microcontrollers while limiting processing-time variability.

## 4. Integration with Geometric Processing

The contribution of our approach goes beyond coordinate compression.

We extended the implementation so that selected geometric operations used in motion planning and control operate directly on quantized Eisenstein positions.

This differs from an architecture in which all geometric calculations are performed using Cartesian coordinates and only the final results are converted into a compact representation.

By integrating the spatial representation into the controller's geometric processing, we investigate whether the mathematical structure itself can contribute to computational efficiency.

## 5. Autonomous Ball Tracking and Prediction

The robot uses a Kalman filter to continuously estimate:

- Ball position.
- Ball velocity.
- Uncertainty associated with the estimated motion.

These estimates support predictive decisions rather than relying exclusively on the most recently observed ball position.

The Kalman filter maintains continuous estimates of ball motion, while the Eisenstein representation is used in selected quantized geometric operations.

The two approaches therefore play complementary roles in the robot's autonomous control system.

## 6. Autonomous Motion Strategies

The controller evaluates two main strategies.

### 6.1 Elliptical Encirclement

In this strategy, the robot follows a planned elliptical trajectory to seek future ball interceptions.

The geometry was adapted so that the reference for the trajectory is the end opposite the ball-capture region, rather than simply the robot's center.

This strategy also considers robot orientation and field boundaries.

The objective is to improve interception positioning while respecting the robot's physical configuration and movement constraints.

### 6.2 Aggressive Attack

In the aggressive attack strategy, the robot aligns its front with the ball or a predicted future interception position and advances to capture it.

This behavior allows the controller to choose a more direct approach when appropriate.

### 6.3 Strategy Selection

The transition between the two strategies considers:

- Estimated arrival time.
- Prediction uncertainty.
- Recency of observations.
- Hysteresis to prevent unnecessary strategy switching.

Hysteresis helps maintain behavioral stability by avoiding frequent transitions between strategies in response to small changes in the estimated state.

The robot can therefore compare movement opportunities, anticipate ball motion, and adapt its behavior autonomously.

## 7. What Makes This Approach Innovative?

We do not claim that Eisenstein numbers, hexagonal lattices, segment trees, or Kalman filters are individually new inventions.

Our contribution lies in **adapting and integrating these established concepts into a compact, hierarchical, and deterministic spatial representation applied to autonomous soccer robot motion planning**.

The proposed structure also differs from a traditional segment tree.

Rather than storing aggregate information over intervals, it uses implicit hierarchical paths to represent two-dimensional positions with progressive spatial refinement.

Its application extends beyond storage because selected controller operations directly use the quantized representation.

The project demonstrates how a mathematical representation can be translated into a low-level embedded implementation through canonical encoding, bit manipulation, and algorithms with bounded computational cost.

## 8. Testing and Validation

During development, we conducted computational tests covering:

- Spatial coverage.
- Quantization accuracy.
- Consistency of canonical paths.
- Equivalence between different encoder implementations.
- Performance comparisons between Cartesian Vec2-based and Eisenstein-based controller implementations.

We also conducted benchmarks to evaluate the computational behavior of the proposed approach.

### Preliminary Findings

Development tests demonstrated the feasibility of the encoding method and showed modest performance improvements in certain scenarios.

The main evaluation metrics are:

| Metric | Evaluation objective |
|---|---|
| Processing time | Compare execution performance |
| Memory usage | Quantify storage and auxiliary memory requirements |
| Spatial error | Evaluate quantization accuracy |
| Interception effectiveness | Assess autonomous behavior during physical tests |

## 9. Applicability to Other Teams

The main transferable insight is that the choice of spatial representation can contribute to the optimization of a control algorithm rather than serving only as a storage format.

Our project provides an example of how to combine:

- Discrete mathematics.
- Implicit hierarchical structures.
- Compact spatial encoding.
- Efficient low-level programming.
- Predictive autonomous motion planning.

These principles may be adapted to other autonomous robots requiring compact localization, controlled spatial precision, and real-time processing on limited hardware.

The project is intended to contribute to knowledge sharing within the RoboCupJunior community and encourage further experimentation with alternative spatial representations for embedded robotics.
