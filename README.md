# Curvetopia: A Journey into the World of Curves

Welcome to Curvetopia, where we bring order and beauty to the world of 2D curves! This project is designed to identify, regularize, and beautify various types of curves in 2D Euclidean space, focusing on closed curves and progressively working with more complex shapes.

## Objective

Our mission is to develop an end-to-end process to convert a set of polylines into regular, symmetric, and completed curves, represented as cubic Bezier curves in SVG format. The project includes:

- **Curve Regularization**: Identify and regularize curves into standard shapes such as straight lines, circles, ellipses, rectangles, rounded rectangles, regular polygons, and star shapes.
- **Exploring Symmetry**: Detect reflection symmetries in closed shapes and fit identical Bezier curves on symmetric points.
- **Completing Incomplete Curves**: Use computer vision techniques to complete curves with gaps due to occlusions, ensuring smoothness, regularity, and symmetry.

## Problem Description

Given polylines representing line art, the goal is to transform them into connected sequences of cubic Bezier curves. The expected output should maintain regularity, symmetry, and completeness as per the requirements outlined below:

1. **Regularize Curves**
    - Straight Lines
    - Circles and Ellipses
    - Rectangles and Rounded Rectangles
    - Regular Polygons
    - Star Shapes
2. **Exploring Symmetry**
    - Reflection Symmetries: Detect lines of symmetry where the shape can be divided into mirrored halves.
3. **Completing Incomplete Curves**
    - Fully Contained: One shape inside another.
    - Partially Contained: Overlapping but connected shapes.
    - Disconnected: Shapes fragmented by occlusions.

## Input & Output

**Input**
- Polyline Data: Provided in CSV format, where each shape is encoded as a list of paths with points.

**Output**
- SVG Files: Curves are converted to cubic Bezier curves and saved in SVG format.
- Completed Curves: Output includes solutions for regularization, symmetry, and completion challenges.

## Visualization

### Reading Polyline Data

```python
import numpy as np

def read_csv(csv_path):
    np_path_XYs = np.genfromtxt(csv_path, delimiter=',')
    path_XYs = []
    for i in np.unique(np_path_XYs[:, 0]):
        npXYs = np_path_XYs[np_path_XYs[:, 0] == i][:, 1:]
        XYs = []
        for j in np.unique(npXYs[:, 0]):
            XY = npXYs[npXYs[:, 0] == j][:, 1:]
            XYs.append(XY)
        path_XYs.append(XYs)
    return path_XYs
```
### Plotting Shapes
**To visualize shapes defined by polylines:**
```python
import numpy as np
import matplotlib.pyplot as plt

def plot(paths_XYs):
    fig, ax = plt.subplots(tight_layout=True, figsize=(8, 8))
    colours = ['blue', 'green', 'red', 'purple', 'orange', 'cyan']
    for i, XYs in enumerate(paths_XYs):
        c = colours[i % len(colours)]
        for XY in XYs:
            ax.plot(XY[:, 0], XY[:, 1], c=c, linewidth=2)
    ax.set_aspect('equal')
    plt.show()
```
### Rasterizing SVG
**To convert SVG files to PNG format:**
```python
import svgwrite
import cairosvg

def polylines2svg(paths_XYs, svg_path):
    W, H = 0, 0
    for path_XYs in paths_XYs:
        for XY in path_XYs:
            W, H = max(W, np.max(XY[:, 0])), max(H, np.max(XY[:, 1]))
    padding = 0.1
    W, H = int(W + padding * W), int(H + padding * H)
    
    dwg = svgwrite.Drawing(svg_path, profile='tiny', shape_rendering='crispEdges')
    group = dwg.g()
    colours = ['blue', 'green', 'red', 'purple', 'orange', 'cyan']
    
    for i, path in enumerate(paths_XYs):
        path_data = []
        c = colours[i % len(colours)]
        for XY in path:
            path_data.append(("M", (XY[0, 0], XY[0, 1])))
            for j in range(1, len(XY)):
                path_data.append(("L", (XY[j, 0], XY[j, 1])))
            if not np.allclose(XY[0], XY[-1]):
                path_data.append(("Z", None))
        group.add(dwg.path(d=path_data, fill=c, stroke='none', stroke_width=2))
    dwg.add(group)
    dwg.save()
    png_path = svg_path.replace('.svg', '.png')
    fact = max(1, 1024 // min(H, W))
    cairosvg.svg2png(url=svg_path, write_to=png_path, parent_width=W, parent_height=H, output_width=fact*W, output_height=fact*H, background_color='white')
```
## Evaluation
Regularization and Symmetry: Evaluated based on the number of identified regular shapes and symmetry in the input.
Occlusion: Validated by rasterizing SVG files and checking the correctness of the completed shapes.

## Getting Started
Clone this repository:
git clone (https://github.com/Harshsinghr/Curvetopia-Adobe-GenSolve-Round22.git)
Install the necessary Python packages:
pip install -r requirements.txt
Run the provided code examples to process and visualize your shapes.
## License
This project is licensed under the MIT License. See the LICENSE file for details.
