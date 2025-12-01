# JS Verlet Cloth Engine 🧶✂️🔥

A high-performance, interactive cloth simulation engine capable of handling 3D geometry, topology changes (tearing), and multi-layer destruction logic. 

Built entirely in **Vanilla JavaScript** with no external dependencies (no Three.js, no WebGL libraries). It utilizes a custom software renderer to project 3D physics onto a 2D HTML5 Canvas.

## ✨ Key Features

* **Physics Core:** Position-Based Dynamics (Verlet Integration) for unconditionally stable simulation.
* **Destructible Mesh:** Real-time topology modification.
    * **Knife Tool:** Ray-casting approximation to cut threads instantly.
    * **Fire Tool:** Heat propagation system that burns and chars fabric before disintegrating it.
* **Material System:** Switchable physics profiles mimicking **Cotton** (damped), **Nylon** (slippery/elastic), and **Denim** (heavy/stiff).
* **Multi-Layer Engine:** Simulates 4 stacked layers of fabric with independent physics and "shielding" logic (you must cut the top layer to reach the bottom).
* **Custom 3D Renderer:** Features a manual perspective projection engine with Z-sorting (Painter's Algorithm) to handle occlusion.

## 🚀 Getting Started

1.  Clone the repository.
2.  Open `index.html` in any modern web browser.
3.  That's it! No build steps or node modules required.

## 🎮 Controls

| Interaction | Action |
| :--- | :--- |
| **Left Click** | Use selected tool (Knife or Lighter) |
| **Right Click** | Grab and drag the cloth (Physics Interaction) |
| **Drag Background** | Orbit Camera (3D Rotation) |
| **Scroll Wheel** | Zoom In / Out |

## 📐 Underlying Physics & Math

This engine moves away from traditional force-based physics (Euler integration) in favor of **Position-Based Dynamics (PBD)**, which is significantly more stable for connected structures like cloth.

### Core Equation: Verlet Integration
Instead of storing velocity explicitly, we calculate it based on the previous position.
$$v = (x_{current} - x_{previous}) \times friction$$
$$x_{next} = x_{current} + v + gravity$$

### The 2D Case
In the initial version, the cloth is modeled as a grid of points on an X/Y plane.
* **Constraints:** Connections between points are "Rigid Distance Constraints." We measure the distance between two points; if they are too far apart, we "teleport" them closer to satisfy the constraint.
* **Topology:** The mesh consists of:
    * *Structural constraints* (Horizontal/Vertical) for shape.
    * *Shear constraints* (Diagonal) to prevent the grid from collapsing into rhombuses.

### The 3D Case & Software Rendering
The final version expands the physics to `x, y, z` coordinates. Since the HTML5 Canvas is a 2D surface, a custom rendering pipeline was implemented:

1.  **Euler Camera:** The world coordinates are rotated around a central pivot using rotation matrices (Pitch and Yaw).
2.  **Perspective Projection:** We calculate a scale factor based on the focal length ($f$) and the point's depth ($z$). Objects further away have a smaller scale factor.
    $$Scale = \frac{f}{f + z}$$
    $$Screen_x = World_x \times Scale + Center_x$$
3.  **The Painter's Algorithm:** To handle depth without a GPU Z-buffer, we:
    * Calculate the average Z-depth of every triangular face.
    * **Sort** all triangles from furthest to closest.
    * Draw them in order. This ensures that foreground layers correctly occlude background layers.

## 📚 References & Papers

This project is an implementation of techniques described in seminal computer graphics papers:

1.  **[Advanced Character Physics](https://web.archive.org/web/20080410171619/http://www.gamasutra.com/resource_guide/20030121/jacobson_01.shtml)** by **Thomas Jakobsen (2001)**.
    * *Context:* The foundational paper describing how IO Interactive (Hitman series) used Verlet integration for stable ragdolls and cloth.

2.  **[Position Based Dynamics](https://matthias-research.github.io/pages/publications/posBasedDyn.pdf)** by **Müller et al. (2006)**.
    * *Context:* Formalizes the math behind manipulating geometry positions directly rather than using spring forces, allowing for the stable simulation of stiff materials like denim.

## 📄 License

MIT License - free to use and modify.
