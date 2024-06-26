# Soft Robot RL-Control based on Elastica Simulator
This project is my final project of NUS ME5423 Topics in Robotics, which is based on the github repository [GazzolaLab/Elastica-RL-control](GazzolaLab/Elastica-RL-control). We fixed bugs and chose the best hyperparameters for the RL algorithms. The results_video folder contains the videos of the our self-trained best controller of each case. I am from Neuro-robotics Project Group 18.

The instruction of running the whole project is also provided. 

> Author: [Tony](https://github.com/ztony0712)
<!-- > Video Presentation -->
[![Soft Robot RL-Control based on Elastica Simulator](https://img.youtube.com/vi/3EWKWqdcM1s/0.jpg)](https://www.youtube.com/watch?v=3EWKWqdcM1s)

## Installation
Clone this repo to your root directory:
```bash
# Clone and go to the directory
cd
git clone https://github.com/ztony0712/Elastica-RL-control-fix-improve
cd Elastica-RL-control-fix-improve

# Install and activate virtual environment
conda create -f environment.yml
conda activate elasticarl
```

## Usage

### 0. **Note**

You don't need to run policy training scripts. We have already selected the best parameter for each case. If you insist to run all of the training scripts, you can follow the instructions in the original repo. However, if you only want to get the best controller for each case, follow the instructions below.

### 1. Set Parameters
**If you want to replicate our parameter settings, skip this step. If you need parameters for specific analysis, check records in our powerpoints**

We recommend you to set your own parameters in the `logging_bio_args.py` file. You can set the parameters before the environment setting line. For example, you can set the total number of timesteps, the timestep per batch, the algorithm name, and switch the TRAIN mode.

```python
# These are example parameters.
args.total_timesteps = 1E6 # Total number of training timesteps
args.timesteps_per_batch = 2048 # Timesteps per batch
args.algo_name = 'PPO' # Choose the algorithm name from ['TRPO', 'PPO', 'SAC', 'DDPG', 'TD3']
args.TRAIN = True # If you want to train the model, set it to True

env = Environment( # Set the parameters before this line
  ...
  ...
)
```

### 2. Run the Training Script
```bash
# For Case 1
python Case1/logging_bio_args.py

# For Case 2
python Case2/logging_bio_args.py

# Case 3, this is only for manually placed 2-control points
python Case3/Case3_main-text/logging_bio_args_OnPolicy.py

# Case 4
python Case4/logging_bio_args.py
```

### 3. Run the Post-Processing Script
Set the `args.TRAIN` to `False` in the `logging_bio_args.py` file. Then, run the step 2 again.

```python
args.TRAIN = False
```

Now, you should get a data folder, a 2D plot, and a 3D plot in the `CaseX` folder. If you want to render the video, you need to follow the next step.

### 4. Visualization
Copy the data folder under the corresponding Case folder of the `visualization` folder. Then, go to the rendering folder and run the scripts.

```bash
python generate_POVray_files.py
./render_frames.sh # This takes a long time
./make_vid.sh
```

The 'render_frames.sh' script can also be customized to adapt to your computer's performance. You can change the following line, but you should search the meaning of the parameters by yourself.

```bash
povray -D -H1080 -W1920 Quality=11 Antialias=on +WT6 $filenameOnly Verbose=off
```

Now, you should get a video in the `CaseX` folder.

## The README below is the original one of the project

## Elastica + RL benchmark cases

This repo provides supplementary code and benchmark data for the paper: [*Elastica: A compliant mechanics environment for soft robotic control*](https://ieeexplore.ieee.org/document/9369003), in IEEE Robotics and Automation Letters.

[Elastica](https://github.com/GazzolaLab/PyElastica) is a simulation environment for simulating assemblies of one-dimensional soft, slender structures using Cosserat rod theory. More information about Elastica is available on the [project website](https://cosseratrods.org). You can install the Python version of Elastica via `pip install pyelastica.` 

In this repo, Elastica is interfaced with [Stable Baselines](https://github.com/hill-a/stable-baselines) to investigate how RL can dynamically control a compliant robotic arm. You can install Stable Baselines via `pip install stable-baselines[mpi]` (note: Stable Baselines only works with TesorFlow <= v1.15).
Five different RL model-free algorithms from the Stable Baselines implementations are used. Two of them are on-policy algorithms: Trust Region Policy Optimization (TRPO) and Proximal Policy Optimization (PPO) and three of them are off-policy algorithms: Soft Actor Critic (SAC), Deep Deterministic Policy Gradient (DDPG), and Twin Delayed DDPG (TD3). Four different cases are considered with detailed explanations given in the paper. 

If you discover any bugs, please open an issue and let us know. We plan to actively maintain and develop these benchmark cases. 

We have provided visualization scripts we use for these cases in the `visualization` folder. Data from hyperparameter tuning is available in the `supplementary_data` folder.

### Case 1: 3D tracking of a randomly moving target
In this case, the arm is continuously tracking a randomly moving target in 3D space. Actuations are only allowed in normal and binormal directions with 6 control points in each direction. 

* To replicate training using different RL algorithms, run `logging_bio_args.py` located in the `Case1/ ` folder. 
You can train policies using the five RL algorithms considered by passing the algorithm name as a command-line argument i.e. `--algo_name TRPO`.
 Also, you can control the total number of training timesteps, the random seed, and the timestep 
per batch as command-line arguments, i.e. `--total_timesteps 1E6`, `--SEED 0`, `--timesteps_per_batch 2048`.
 In addition to that, you can choose a different number of control points or torque scaling factor by changing the 
`number_of_control_points` and `alpha` variables inside `logging_bio_args.py` respectively.  
* To replicate the hyperparameter tuning, run the code `Case1/policy_training_script.py`. 
Note that the number of CPUs should be edited appropriately in the script. Runtime is 12-24 hours per individual case.
* Code for initializing the Elastica simulation environment is located in `Case1/set_environment.py`. 
Specific details on how Case 1 was implemented are in this file. 
* Post-processing scripts are located in `Case1/post_processing.py`



### Case 2: Reaching to randomly located target with a defined orientation
In this case, the arm is reaching the randomly positioned stationary target, while re-orienting itself to match the orientation
of the target. Actuations are allowed in normal, binormal, and tangent directions with 6 control points in each direction.

* To replicate training using different RL algorithms, run `logging_bio_args.py` located in the `Case2/ ` folder. 
You can train policies using the five RL algorithms considered by passing the algorithm name as a command-line argument 
i.e. `--algo_name TRPO`. Also, you can control the total number of training timesteps, the random seed, and the timestep 
per batch as command-line arguments, i.e. `--total_timesteps 1E6`, `--SEED 0`, `--timesteps_per_batch 2048`. 
In addition to that, you can choose a different number of control points or torque scaling factor by changing the 
`number_of_control_points` and `alpha` variables inside `logging_bio_args.py` respectively.  
* To replicate the hyperparameter tuning, run the code `Case2/policy_training_script.py`. 
Note that the number of CPUs should be edited appropriately in the script. Runtime is 12-24 hours per individual case. 
* Code for initializing the Elastica simulation environment is located in `Case2/set_environment.py`. 
Specific details on how Case 2 was implemented are in this file. 
* Post-processing scripts are located in `Case2/post_processing.py`


### Case 3: Underactuated maneuvering between structured obstacles
In this case, the arm is reaching a stationary target placed behind an array of eight obstacles with an opening through
which the arm must maneuver to reach the target. Target is placed in the normal plane so that only in-plane actuation is
required. Thus actuation only in the normal direction is allowed. Case 3 has two subcases. First one is training using 
2 manually placed control points at 40% and 90% of the arm and the second one is training using 2, 4, 6, and 8 
equidistant control points. Code for manually selected control
points are located in `Case3/Case3_main-text/` folder and code for equidistant control points are
located in `Case3/Case3_SI-ctrl_pts/`.

* To replicate the manually placed two control points training:
   * Run `Case3/Case3_main-text/logging_bio_args_OnPolicy.py` and run
    `Case3/Case3_main-text/logging_bio_args_OffPolicy.py` for on-policy and off-policy algorithms
     respectively. You can train policies using the five RL algorithms considered by passing the algorithm name as a 
     command-line argument i.e. `--algo_name TRPO`. Also, you can control the total number of training timesteps, 
     the random seed, and the timestep per batch as command-line arguments, i.e. `--total_timesteps 1E6`, `--SEED 0`, `--timesteps_per_batch 2048`. 
     In addition to that, you can choose a different torque scaling factor by changing the `alpha` variable inside 
    `logging_bio_args_OnPolicy.py` or  `logging_bio_args_OffPolicy.py` depending on the policy. The number of control points is fixed for this case and it is two. 
    * To replicate the hyperparameter tuning, run the code `Case3/Case3_main-text/policy_training_script_OnPolicy.py`
    or `Case3/Case3_main-text/policy_training_script_OffPolicy.py`. 
    Note that the number of CPUs should be edited appropriately in the script. Runtime is 3-4 hours per individual case.  
    * Code for initializing the Elastica simulation environment is located in `Case3/Case3_main-text/set_environment.py`. 
    Specific details on how the first subcase of Case 3 was implemented are in this file. 
    * Post-processing scripts are located in `Case3/Case3_main-text/post_processing.py`.

* To replicate the equidistantly placed control points training:
   * Run `Case3/Case3_SI-ctrl_pts/logging_bio_args.py`. You can train policies using the five RL
    algorithms considered by passing the algorithm name as a command-line argument i.e. `--algo_name TRPO`. 
    Also, you can control the total number of training timesteps, the random seed, and the timestep per batch as 
    command-line arguments, i.e. `--total_timesteps 1E6`, `--SEED 0`, `--timesteps_per_batch 2048`. In addition to that,
     you can choose a different number of control points or torque scaling factor by changing the `number_of_control_points` 
     and `alpha` variables inside `logging_bio_args_OnPolicy.py` or  `logging_bio_args_OffPolicy.py`respectively. 
    * To replicate the hyperparameter tuning, run the code `Case3/Case3_SI-ctrl_pts/policy_training_script.py`.
    Note that the number of CPUs should be edited appropriately in the script. Runtime is 3-4 hours per individual case.  
    * Code for initializing the Elastica simulation environment is located in `Case3/Case3_SI-ctrl_pts/set_environment.py`. 
    Specific details on how the second subcase of Case 3 was implemented are in this file. 
    * Post-processing scripts are located in `Case3/Case3_SI-ctrl_pts/post_processing.py`. 


### Case 4: Underactuated maneuvering between unstructured obstacles
In this case, the arm is reaching a stationary target by maneuvering around an unstructured nest of twelve randomly located
obstacles. Actuation for this case is similar to Case 3, using two manually placed control points at 40% and 90% of the arm. 
Different than Case 3 actuations in normal and binormal directions are allowed. 

* To replicate training using different RL algorithms, run `logging_bio_args.py` located in the `Case4/ ` folder. 
You can train policies using the five RL algorithms considered by passing the algorithm name as a command-line argument 
i.e. `--algo_name TRPO`. Also, you can control the total number of training timesteps, the random seed, and the timestep 
per batch as command-line arguments, i.e. `--total_timesteps 1E6`, `--SEED 0`, `--timesteps_per_batch 2048`. 
In addition to that, you can choose a different torque scaling factor by changing the `alpha` variable inside 
`logging_bio_args.py`. The number of control points is fixed for this case and it is two. 
* To replicate the hyperparameter tuning, run the code `Case4/policy_training_script.py`. 
Note that the number of CPUs should be edited appropriately in the script. Runtime is 6-8 hours per individual case. 
* Code for initializing the Elastica simulation environment is located in `Case4/set_environment.py`. 
Specific details on how Case 2 was implemented are in this file. 
* Post-processing scripts are located in `Case4/post_processing.py`


## Citation
We ask that any publications which use these benchmark cases cite the original paper:

Naughton, Sun, Tekinalp, Parthasarathy, Chowdhary and Gazzola, <strong>Elastica: A compliant mechanics environment for soft robotic control</strong>, IEEE Robotics and Automation Letters, 2021. doi: [10.1109/LRA.2021.3063698](https://doi.org/10.1109/LRA.2021.3063698)
```
@article{Naughton2021,
  author={Naughton, Noel and Sun, Jiarui and Tekinalp, Arman and Parthasarathy, Tejaswin and Chowdhary, Girish and Gazzola, Mattia},
  journal={IEEE Robotics and Automation Letters}, 
  title={Elastica: A compliant mechanics environment for soft robotic control}, 
  year={2021},
  volume={6},
  number={2},
  pages={3389-3396},
  doi={10.1109/LRA.2021.3063698}
}
```



