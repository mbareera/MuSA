# MuSA: The Multiscale Snow data Assimilation System   
  
The Multiscale Snow Assimilation System (MuSA), is a flexible data  assimilation toolbox for experimental and operational snowpack  reanalysis development. MuSA was designed to fuse gridded observations  with and ensemble of simulations generated by the Flexible Snow Model  [(FSM2)](https://github.com/RichardEssery/FSM2) by using different  Bayesian based data assimilation algorithms. 

![alt text](https://github.com/ealonsogzl/MuSA/blob/master/img/PBS_animation.gif)
<em> Figure 1: Comparison between open loop and updated simulation after assimilating UAV snow depth retrievals at 5m resolution </em>
### Inputs  
  
The inputs of MuSA are composed by meteorological forcing and  observations to be assimilated. Both the forcing and observations must  share the **same geometry**, with the same resolution and number of  cells in the latitudinal and longitudinal axes, and should be probided in the [netCDF](https://www.unidata.ucar.edu/software/netcdf/) format. In this version, the meteorological forcing must be provided in an hourly timestep. Optionally it is  possible to provide a mask with the same geometry of the mandatory input  files to avoid to run MuSA over certain cells of your domain. The  meteorological forcing needed for running MuSA is composed by: 
- Incident shortwave radiation (W m<sup>-2</sup>)
- Incident longwave radiation ( W m<sup>-2</sup>)
- Precipitation  (Kg<sup>-2</sup> m<sup>-2</sup> s<sup>-1</sup>) 
- Temperature (K) 
- Relative Humidity (%) 
- Wind speed  (m s<sup>-1</sup>) 
- Atmospheric pressure (Pa) 
  
In its current version MuSA provides support for assimilating different  variables. Note that it is possible to provide more than one of the  following variables at the same time, i.e. MuSA has support for joint  assimilation experiments. In its current version, MuSA is able to assimilate: 
- SWE (mm) 
- Snow depth (m) 
- Land/Snow surface temperature (K) 
- Snow cover area (binary) 
- Fractional snow cover area (-) 
- Albedo (-) 

The support of other variables like liquid water content, density, ice content etc.  could be relatively easily implemented on demand. 
  
### Data assimilation algorithms
There are different different data assimilation algortihms and resampling algorithms implemented in MuSA.  Some testing should be done when developing data assimilation experiments, as the performance may be different depending on the problem to solve, and regarding the literature there is not a clear winner. Also, the computational cost will be different, and may be a strong conditioner in some situations.

Filters:
-   Particle Filter (PF)
-   Ensemble Kalman filter (EnKF)
-   Iterative ensemble Kalman filter (IEnKF)

Smoothers:
-   Particle batch smoother (PBS)
-   Ensemble smoother (ES)
-   Iterative ensemble smoother (IES)
    
Resampling (for particle filters only):
-   Bootstrapping
-   Residual resampling
-   Stratified resampling
-   Systematic resampling
-   Redraw from a normal approximation of the posterior

### Outputs
The outputs of MuSA are composed by simple .csv files containing the following information:
-  **OL_lonid_latid**: This file contains the open loop simulation (snow simulation without any assimilation).
-  **updated_lonid_latid**: This file contains the updated simulation after the assimilation, i.e. weighted mean of the ensemble of simulaitons.
-  **sd_lonid_latid**: This file contains the weighted standard deviation of the ensemble after the assimilation.
-  **DA_lonid_latid**: This fille contains information about the observed variables and perturbation parametesr used to generate the ensemble.

**lonid** and **latid** are the longitude and latitude ids of each cell of the simulation.

Aditionaly it is possible to store the ensembles generated for each cell. This is an optional feature as it may be a bit memory consuming. However It may be usefulll in some circustancies specially for advanced users. The ensembles will be stored as pickle objects, and will be composed by python lists containing [Ensemble objects](https://github.com/ealonsogzl/MuSA/blob/master/modules/internal_class.py).

### Usage

This version only works on GNU/Linux (and therefore Mac) based platforms. I guess it should be possible to run MuSA on Windows with an appropiate setup but we haven't tested it yet. MuSA relies on python3 with the usual scientific libraries (numpy, pandas, scipy...) and netCDF4 installed. You will also need to have a fortran compiler like gfortran in the path. The fortran compiler is hardcoded [here](https://github.com/ealonsogzl/MuSA/blob/master/FSM2/compil.sh#L11). The easiest way to do this is to generate a dedicated conda environment. You can use the [MuSAenv.yml](https://github.com/ealonsogzl/MuSA/blob/master/MuSAenv.yml) file of the repository to create the conda environtment:

```
conda env create --name MuSAenv --file=MuSAenv.yml
```


Then for running MuSA simply:

```
conda activate MuSAenv
python main.py
```

This command should run the reproducible example included in the repository. This example contains all the information needed by MuSA. It is composed by few cells containing meteorological forcing and drone SfM derived snowdepth information. To change the configuration of MuSA, you should modify the [config.py](https://github.com/ealonsogzl/MuSA/blob/master/config.py) file. Also it is posible to modify the way MuSA generates de ensemble by modifing the [constants.py](https://github.com/ealonsogzl/MuSA/blob/master/constants.py) file.
An [example script](https://github.com/ealonsogzl/MuSA/blob/master/run_PBS.pbs) is also provided to run MuSA in distributed supercomputing facilitites using PBS (Portable Batch System, not Particle Batch Smoother :wink:) arrays.
### How to cite
#### MuSA
#### FSM2
-  Mazzotti, G., Essery, R., Moeser, C. D., and Jonas, T.: Resolving small-scale forest snow patterns using an energy balance snow model with a one-layer canopy. Water Resour. Res., 56, https://doi.org/10.1029/2019WR026129, 2020.
-  Essery, R.: A factorial snowpack model (FSM 1.0), Geosci. Model Dev., 8, 3867–3876, https://doi.org/10.5194/gmd-8-3867-2015, 2015. 
#### Related references
-  Alonso-González, E., Gutmann, E., Aalstad, K., Fayad, A., Bouchet, M., and Gascoin, S.: Snowpack dynamics in the Lebanese mountains from quasi-dynamically downscaled ERA5 reanalysis updated by assimilating remotely sensed fractional snow-covered area, Hydrol. Earth Syst. Sci., 25, 4455–4471, https://doi.org/10.5194/hess-25-4455-2021, 2021.
-  Fiddes, J., Aalstad, K., and Westermann, S.: Hyper-resolution ensemble-based snow reanalysis in mountain regions using clustering, Hydrol. Earth Syst. Sci., 23, 4717–4736, https://doi.org/10.5194/hess-23-4717-2019, 2019. 
-  Aalstad, K., Westermann, S., Schuler, T. V., Boike, J., and Bertino, L.: Ensemble-based assimilation of fractional snow-covered area satellite retrievals to estimate the snow distribution at Arctic sites, The Cryosphere, 12, 247–270, https://doi.org/10.5194/tc-12-247-2018, 2018.

