---
title: "Interactive Math Visualisations for the Browser with Rust, WebGL, and WebAssembly"

author_profile: true
sidebar:
  nav: sidebar
  sticky: false

categories: 
  - Tech Projects
tags: 
  - Rust
  - Web Assembly
  - WebGL
  - Math
layout: single

# Table of Contents
toc: true
toc_label: "Contents"
toc_icon: "compass"
toc_sticky: true
toc_levels: 1, 2, 3
---
<br>
Introduction
Visualising complex mathematics interactively in the browser? Need high-performance due to the heavy renderings, and traditional JavaScript wouldn't be enough.

## Project Overview
Initial Objective: visualising complex analysis and conformal mappings <br>
Scope: Is part of a larger extensible framework that could handle various mathematical visualisations like fractal geometry and differential equations. <br>
Features
- Real-time interaction with math concepts
- Visualisations
- High-performance rendering using WebGL and WebAssembly

## Why Rust, WebAssembly, and WebGL?
Rust? Memory safety and performance <br>
WebAssembly? near-native speed for web applications esp important for complex computations <br>
WebGL? choice for rendering high-quality real-time graphics, interfaces with Rust via WebAssembly <br>

### WebGL, WebAssembly, and Rust
Link to post

## Technical Challenges
Real-time Performance <br>
WebAssembly Limitations (limited debugging?) <br>
GPU Programming: shader programming and handling WebGL in Rust, need efficient data transfer between WebAssembly and WebGL <br>
Memory Management: need efficient memory use when managing large datasets or handling complex computations <br>
Interactive UI: zooming, panning, and parameter adjustments? <br>

## Scalability and Extensibility
Modularising visualisation for scaling: extend to additional math concepts beyond complex analysis <br>
Other topics like fractals, 3d geometry or physics simulations? <br>
Optimisations for scaling? offload more computations to WebGL shaders, improve memory efficiency, and use multi-threading in WebAssembly <br>

## Project Architecture
Modular components: visualisation modules for scalability, rendering engine, UI, etc <br>
Visualisation pipeline: data flow from the math engine through the Rust-generated WebAssembly to the WebGL renderer <br>
How user interactions are handled? <br>
Any bottlenecks for performance? <br>
Architecture diagram <br>

Demo?