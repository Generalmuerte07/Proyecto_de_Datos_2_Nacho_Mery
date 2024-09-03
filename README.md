# Soft Robots Design Optimization Toolbox using SOFA Framework for Simulation
[![SOFA](https://img.shields.io/badge/SOFA-on_github-orange.svg)](https://github.com/SofaDefrost/sofa) 

This software toolkit contains components for exploring parametric design of any SOFA scene.
We provide a unified framework for implementing a single parametric Sofa scene and use it both for multi-objective design optimization and model-based control. 

This toolkit is provided with the example of the optimization of a soft finger parametric design.
This example illustrates how to couple heuristic search and automatic mesh generation for efficiently exploring the soft finger geometry. We also introduce scripts for automatically generating the molds necessary for manufacturing a given design.


<img src="/images/Intro_Toolbox.png" width="900">

More examples will be available in the future.

# Table of Contents
1. [Installation](#installation)
2. [Quick Start](#quickstart)
      1. [Baseline Simulation](#baselinesim)
      2. [Sensitivity Analysis](#sensitivityanalysis)
      3. [Design Optimization](#designoptimization)
      4. [Simulating Results from Optimization](#resultsim)
4. [Examples](#examples)
      1. [Tutorial for Creating Parametric Designs with Gmsh](#gmshtutorial)
      2. [Design Optimization of a Sensorized Finger](#sensorizedfinger)
      3. [Design Optimization of a Contact-Aided Manipulator](#contactaidedfinger)
5. [Known Issues](#knownissues)
6. [Citing](#citing)


# Installation <a name="installation"></a>

## Python requirements
Python3 is needed to make use of toolbox.
The required basic python libraries can be installed with the following bash command:
```bash
pip install pathlib importlib numpy logging plotly
```

Additionally, the provided example expects an installation of the following libraries:
* [Gmsh](https://gmsh.info/) for automatic mesh generation.
* [Optuna](https://github.com/optuna/optuna) implementing algorithms for heuristic-based optimization.

Both this libraries can be installed using the following bash command:
```bash
pip install gmsh==4.11.1 optuna==2.10.0 SQLAlchemy==1.4.44
```

## SOFA and mandatory plugins
This toolbox was tested with the SOFA v22.12 installation. 
The following plugins are mandatory:
* [SofaPython3](https://github.com/sofa-framework/SofaPython3)
* [SoftRobots](https://github.com/SofaDefrost/SoftRobots)

Please refer to the following [tutorial](https://www.sofa-framework.org/community/doc/getting-started/build/linux/) for a complete overview of the installation process.



# Quick Start <a name="quickstart"></a>

## Soft Robot Modeling for Design Optimization
A soft robot model is described through a set of different scripts:
* A parametric SOFA scene called after the model name. This scene should also reimplement a Controller class inheriting from BaseFitnessEvaluationController to describe the soft robot's control strategy for evaluating a given parametric design.
* A Config class inheriting from BaseConfig describing the design variables and optimization objectives.

## User Interface
In this section we introduce the main commands for using the toolbox with the SensorFinger example. For testing these commands, first open a command prompt in the project directory, then type the command provided bellow. A list of all available commands can be read in the main.py file.

### Testing a baseline SOFA scene <a name="baselinesim"></a>
For running a parametric scene without optimization, the following command is available:
```bash
python3 main.py -n SensorFinger -op 0 -sd -so ba 
```
- -n, --name: name of the soft robot.
- -op, --optimization_problem: reference to the configuration describing a given optimization problem for a soft robot parametric design. This is an optional parameter for running a baseline simulation. For a same soft robot, several optimization configurations can be implemented considering different design variables or optimization objectives. After the simulation is run, the cost function related to the optimization problem will be evaluated and printed on the console. In the case of the SoftFinger, the available optimization configurations are found in the subfolder "Models/SensorFinger/OptimizationConfigs/". If you select a non-existing configuration, the script will run with a default value. 
- -sd, --simulate_design: call to the simulation script in the SOFA GUI
- -so, --simulation_option: simulation option. For baseline simulation, we have to specify the option "ba" [Optional, default=ba]

### Sensitivity Analysis <a name="sensitivityanalysis"></a>
Running a sensitivity analysis for measuring the local impact of each design variable on the optimization objectives is performed through:
```bash
python3 main.py -n SensorFinger -op 0 -sa -nsa 2
```
- -n, --name: see above
- -op, --optimization_problem: see above.
- -sa, --sensitivity_analysis: call to the sensitivity analysis script.
- -nsa, --n_samples_per_param: Number of point to sample for each design variable [Optional, default=2].

### Design Optimization <a name="designoptimization"></a>
Design optimization of a parametric design is launched using:
```bash
python3 main.py -n SensorFinger -op 0 -o -ni 100
```
- -n, --name: see above
- -op, --optimization_problem: see above
- -o, --optimization: call to the design optimization script
- -ni, --n_iter: Number of design optimization iterations [Optional, default=10]

#### Multithreading Feature <a name="multithreading"></a>
To start a multithreaded design optimization, simply run the design optimizaiton command in several different terminals. 
The number of design optimization iterations "ni" provided is then the number of iterations for each process.

### Simulate a Design obtained through Optimization <a name="resultsim"></a>
For selecting and visualizing any design encountered during design optimization in the SOFA GUI, consider the following command: 
```bash
python3 main.py -n SensorFinger -op 0 -sd -so fo
```
- -so, --simulation_option: simulation option. For choosing a specific design encountered during design optimization, we have to specify the option "fo" [Optional, default=ba]

Once launched, a command prompt will ask you the id of the design to simulate.


# Examples <a name="examples"></a>

## Gmsh Tutorial for Parametric Construction of a Deformable Pawn with Accordion Structure and Internal Cavity <a name="gmshtutorial"></a> 

In the folder ["GmshTutorial"](GmshTutorial) there is a tutorial explaining step-by-step how a parametric design can be implemented with Gmsh using the Python 3 bindings. In the same subfolder, there is a more detailed README about the tutorial. The following are some images extracted from design generation steps and SOFA simulation from the aforementioned tutorial:

<img src="/images/GmshTuto_Step3.png" width="200"> <img src="/images/GmshTuto_Step5.png" width="200"> <img src="/images/GmshTuto_Step7.png" width="200"> <img src="/images/GmshTuto_SOFASim.png" width="200" height="260">


## Design Optimization of a Sensorized Finger <a name="sensorizedfinger"></a> 
The [Sensorized Finger](Models/SensorFinger) is a cable-actuated soft finger with pneumatic chambers located at the joints. These chambers are used as sensors. The measurement of their volume change enables finding the Sensorized Finger actuation state through inverse modeling. The robot parameterization as well as our results are described in this [article](https://arxiv.org/pdf/2304.07260.pdf). We also provide scripts for automatic mold generation for manufacturing any optimized robot.

![Alt text](/images/SensorizedFingerOverview.png)

As the work on the toolbox is still in progress, there may be slight changes with the results from the article. 

## Design Optimization of the Tripod Finger, a Contact-Aided Manipulator <a name="contactaidedfinger"></a> 
The [Contact-Aided Manipulator](Models/TripodFinger) is composed of three soft fingers actuated by one servomotor each. The aim is to make use of localized self-contacts for better energy consumption and grasping performances. The robot parameterization as well as our results are described in this [article](https://theses.hal.science/CRISTAL-DEFROST/hal-04482015v1). 

![Alt text](/images/ContactAidedFinger_Overview.png)



# Known Issues <a name="knownissues"></a> 

During the exploration of design parameters, it is possible that no meshes can be created by Gmsh. In fact, it is possible that the call to Gmsh will not automatically terminate. In this case, the whole optimization loop will be stalled, making it necessary to abort and restart or to keep the partial results only. We are currently working on fixing this issue. 

# Citing <a name="citing"></a> 
If you use the project in your work, please consider citing it with:
```bibtex
@misc{navarro2023open,
      title={An Open Source Design Optimization Toolbox Evaluated on a Soft Finger}, 
      author={Stefan Escaida Navarro and Tanguy Navez and Olivier Goury and Luis Molina and Christian Duriez},
      year={2023},
      journal={IEEE Robotics and Automation Letters},
      volume={8},
      number={9},
      pages={6044--6051},
      publisher={IEEE},
      doi={10.1109/LRA.2023.3301272},
}
```
