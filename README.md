# ScanChessOnline

Work in Progress!

## Chessboard Detection

### 1. Line detection and processing  

a. Use OpenCV's Line Segment Detector to detect line segments. I found that Line Segment Detector produces less false positives and is more robust to noise than Hough Lines.

b. Use an algorithm inspired by [this paper](https://arxiv.org/abs/1708.03898) to merge nearly colinear lines. The idea is to use a heuristic to determine whether 2 lines should be merged (the heuristic involves shortest distance between endpoints of one line to the other line, length of the lines, and some hyperparameters). Disjoint Set Union data structure is used for fast merging and lookup of sets.

c. Extend merged lines to infinity, and find all intersections. Then, crop out a 32 by 32 centered around that intersection point for classification.

### 2. Lattice point classification

a. Use a template matching based method based on [this paper](https://iopscience.iop.org/article/10.1088/1757-899X/563/5/052093/pdf). The idea is to define kernels representing lattice points of chessboard patterns and calculate the normalized cross correlation of that kernel applied to the intersection image. If the max of the result is greater than some predefined threshold, then the intersection is a chessboard lattice point.

I also experimented with neural networks for this task, but inference on ~800 images was too slow for my needs. I might come back to this and try optimizing a neural network in the future.

### 3. Scoring

a. Heuristically try sets of 4 lines that may determine the borders of a chessboard. Project lattice points onto the frame of the 4 corners defined by the intersection 4 lines, and define a scoring function based on shortest distances from a unity grid to projected lattice points. The lines with highest score determine the chessboard.

Example detections:
![example1](https://user-images.githubusercontent.com/55104633/227004743-4be81201-50b1-4b9b-9bbf-0ff2130fa906.jpg)
![example2](https://user-images.githubusercontent.com/55104633/227005514-59f3c788-3a74-4d4b-b81b-c91bee614a38.jpg)
