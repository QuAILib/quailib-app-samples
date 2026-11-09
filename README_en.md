# Tutorials for Developing Applications with Quantum-AI Hybrid Technologies

English | [日本語](README.md)

This repository publishes tutorials for developing and validating applications that combine quantum computing and AI. Through concrete examples, you can learn in practice how to use quantum-AI hybrid technologies, and you can run and evaluate them on ABCI-Q, the quantum-classical computing platform operated by AIST (National Institute of Advanced Industrial Science and Technology).

# Contents

The tutorials are provided as Jupyter Notebooks so that application developers and researchers can understand the basics of development with quantum-AI hybrid technologies. Through concrete sample code, you can experience the actual flow of development and validation.
The tutorials cover the following common quantum-AI hybrid libraries.
1. SWIFT-FMQA (black-box optimization)
   A library that extends FMQA (Factorization Machine with Quadratic-optimization Annealing), a black-box optimization method, so that it can be applied to complex optimization problems [1]. Developed by the Tanaka Laboratory at Keio University.
   https://github.com/Shu-Tanaka-Group/amplify-bbopt-ext
2. Qukit-learn (clustering)
   A library with a Scikit-learn-compatible interface that implements clustering methods based on quantum machine learning in a quantum-AI hybrid environment. Developed by the Komatsu Laboratory at Tohoku University.
   https://github.com/kazukomatsu/qukit-learn-nedo
3. QuantumCircuitCutting (quantum circuit cutting)
   A library that divides a large quantum circuit so that it can be run on a small quantum computer. Developed by PwC Consulting LLC.
   https://github.com/QuantumCircuitCutting

These tutorials are advanced tutorials for building concrete applications with these common libraries. Basic usage of each library is described on its own site.
The tutorials also use Qudas, an input/output conversion library (developed in this project), and show how to handle the different data formats of different quantum/AI frameworks in a unified way. This makes it possible to compare results across multiple solvers and to use them for benchmarking.


## Folder structure

```text
quailib-app-samples/
├─ README.md
├─ README_en.md
├─ LICENSE
├─ .gitignore
├─ tutorials/
│  ├─ cirquit_cut/
│  │  └─ transportation_curcuit_cut_tutorial.ipynb
│  └─ qklearn_fmqa/
│     ├─ README.md
│     ├─ README_en.md
│     ├─ clustering_qklearn_fmqa_tutorial.ipynb
│     ├─ clustering_qklearn_fmqa_tutorial_en.ipynb
│     ├─ manifests/
│     └─ requirements-lock.txt
└─ docs/
```

For details of `tutorials/qklearn_fmqa/` (setup, preparing MVTec AD, how to run), see [tutorials/qklearn_fmqa/README_en.md](tutorials/qklearn_fmqa/README_en.md).

The `qklearn_fmqa` tutorial is available in Japanese and English. The `cirquit_cut` tutorial (`transportation_curcuit_cut_tutorial.ipynb`) is available in Japanese only.

## Usage
The tutorials are provided as Jupyter Notebooks. You can run each notebook in your local environment or with the ABCI-Q Open OnDemand feature (https://g-quat-abciq.github.io/abciq-docs/ja/open-ondemand/, Japanese page).


## Acknowledgments
This work was obtained as a result of a project subsidized by the New Energy and Industrial Technology Development Organization (NEDO).
