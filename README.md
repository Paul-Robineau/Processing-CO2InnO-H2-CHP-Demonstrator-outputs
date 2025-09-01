# Processing CO2InnO-H2-CHP-Demonstrator outputs

This work has been done as part of the [CO2InnO project](https://co2inno.com/), which was financed by the European Union through [Interreg Upper Rhine 2021–2027, A1.3](https://www.interreg-rhin-sup.eu/projet/co2inno-laboratoire-vivant-pour-une-region-dinnovation-pilote-neutre-en-co2-developpement-de-solutions-energetiques-et-de-mobilite/). 

The associated scientific article will be linked as soon as it is published.

## Context

This repository contains the Jupyter Notebooks allowing to process numerical outputs of a TEA (Techno-Economic Assessment) OpenModelica model which code is also available in Github under the name [CO2InnO-H2-CHP-Demonstrator](https://github.com/IKKUengine/CO2InnO-H2-CHP-Demonstrator). It is used for the study of decentralized energy systems (DES) based on hydrogen use (H₂) and using H₂ in an internal combustion engine for combined heat & power (ICE-CHP). 

The processing is required order to extract relevant data for a LCA (Life-Cycle Assessment) study of the system modeled from a TEA approach and allow to interactively generate relevant visualizations. The results dataset of the LCA and the dedicated processing notebooks are hosted in a companion repository.

## System model boundaries

The investigated system is centered arround the ICE-CHP in a context of heat-lead functionning where electricity exchanges with the grid are possible. It comprises three interconnected subsystems: electrical, thermal, and hydrogen.

- Electrical system
    - PV - Pholtovoltaic
    - WT - Wind turbine
    - BAT - Battery
- Heating system
    - GSHP - Ground source heat pump
    - TES - Thermal energy storage
- Hydrogen system
    - PEMEL - Proton exchange membrane electrolyzer
    - H2C - Hydrogen compressor
    - H2S - Hydrogen storage

<img src="img/SystemBoundaries.png">

## Scenario dimensioning

As the central element, the heating system was dimensioned based on economic optimization. Several scenarios were designed to assess how variations in hydrogen storage (H2S) capacity affect the feasibility and efficiency of the H₂-ICE-CHP-based DES [Beerlage et al., 2024](https://doi.org/10.3384/ecp20780 ). H2S was selected as a key variable due to its practical and economic constraints, including large volume requirements and high capital costs, which present major barriers in real-world implementation. By varying storage volumes, the study sought to identify an optimal size that balances hydrogen production and consumption without increasing emissions or inefficiencies. Given the system’s heat-oriented design, the primary goal was to meet thermal demand; electricity production via photovoltaic (PV) and wind turbines (WT) was primarily intended to support hydrogen generation rather than grid supply. This focus on heat demand influenced both control strategies and the sizing of key components such as the CHP unit and hydrogen storage. Scenario variables are detailed in the Table below.

| Scenario      | ICE-CHP el (kW) | ICE-CHP th (kW) | PV (MWp) | WT (MWp) | BAT (kWh) | GSHP (kW) | TES (m3) | PEMEL (MW) | H2S (m3)                      |
|---------------|-----------------|-----------------|----------|----------|-----------|-----------|----------|------------|-------------------------------|
| PV + WT + BAT | 38              | 53,7            | 0,9      | 0,5      | 500       | 197       | 5        | 0,5        | 1000, 500, 100, 50, 30, 10, 1 |
| WT + BAT      | 38              | 53,7            | 0        | 0,5      | 500       | 197       | 5        | 0,5        | 1000, 500, 100, 50, 30, 10, 1 |
| PV + BAT      | 38              | 53,7            | 1,8      | 0        | 500       | 197       | 5        | 0,5        | 1000, 500, 100, 50, 30, 10, 1 |
| PV            | 38              | 53,7            | 1,8      | 0        | 0         | 197       | 5        | 0,5        | 1000, 500, 100, 50, 30, 10, 1 |

## Data origin and uncertainties

As part of the [CO2InnO project](https://co2inno.com/), the city of Offenburg (Germany) provided hourly heat and electricity demand data over one year for five buildings. These buildings were modeled as a single system with shared infrastructure, based on simplifying assumptions agreed upon by project partners. Weather inputs were sourced from the open-source CBA Clima tool [Betti et al., 2024](https://doi.org/10.1007/s12273-023-1090-5), using data from nearby Strasbourg (France) due to the absence of a dedicated Offenburg dataset. Mass and energy flows derived from simulations depend on quality of the OpenModelica model. Since the system design centers on a ICE-CHP unit and a proton-exchange membrane electrolyzer (PEMEL), these components’ models were validated in the original TEA study [Beerlage et al., 2024](https://doi.org/10.3384/ecp20780 ). The average relative deviation was ~10% between model and lab measurements for the PEMEL, and 1-4% compared to manufacturer specifications or the Hofner model for the ICE-CHP unit.

## Structure & How to use

### Link with the Zenodo repository

The `OM_xxx` zip files in the Zenodo repository (link to be added shortly) contains the outputs of the OpenModelica TEA model in different versions, before and after processing for transparency. The `Jupyter-Notebooks` zip file contains the different notebooks using Python code to process the files, and are the same as the ones which are stored in this Github repository. These notebooks have in-depth internal documentation to help new users.

### When doing the whole pipeline

1. First, the export by OpenModelica lead to anomalities like duplicate rows. The original outputs are thus processed by the `om_output_cleaning` Jupyter Notebook to produce the cleaned outputs, and then can also be used to produce the extracted outputs which are used in the second step.
2. The extracted outputs can be processed with the `balance_of_flows` notebook to derive the flows' allocation between subsystem components (indispensable for LCA study).
3. The `sankey_diagrams_only` notebook is an independant version of the end of the `balance_of_flows` notebook, dedicated to flow visualization purposes.

Note : You should extract all the zip files in the same working folder in order to keep the relative links between notebooks and data files.

<img src="img/SankeyDiagramsExample.png">

### Relevance of the pkl folder

These `pickle` files contains the different subdatasets used in the pipeline after OM outputs cleaning and extraction. It allows users to only execute some part of the `balance_of_flows` & `sankey_diagrams_only` notebooks according to their need. The original datasets can be found in the dedicated Zenodo repository (a link will be added shortly after publication of the Zenodo repository).

## Requirements

### Python environment manager

Note: the notebooks were tested on Python 3.11.

We suggest you to use Miniconda:

> Miniconda is a free, miniature installation of Anaconda Distribution that includes only conda, Python, the packages they both depend on, and a small number of other useful packages.

[Miniconda Installation Guide](https://www.anaconda.com/docs/getting-started/miniconda/main)

### Setting up the environment

Within your Python environment manager console (like miniconda console, or anything that suits you):

Create a new Conda environment with all required libraries (in this example named `da` for "data analysis"):

```
conda create  -n da -c conda-forge os pandas numpy tdqm matplotlib seaborn jupyterlab
```
(Add any required library)

Or, create the Conda environment only:

```
conda create -n da -c conda-forge
```

And install the libraries afterwards, after activating the environment:

```
conda activate da
```

```
conda install os pandas numpy tdqm matplotlib seaborn jupyterlab
```
(Add any required library)

Some libraries may require you to use  the `pip` manager instead of conda. In that case, use the command:

```
pip install name_of_library
```

## Use the Jupyter Notebooks

1.  Activate the environment:

```
conda activate da
```

2. Launch Jupyter Lab:

```
jupyter-lab
```
You can select the Notebook you want to use by navigating in the directory where you downloaded the Notebooks.

They are thoroughly documented to allow you to understand what does each bloc of code.
