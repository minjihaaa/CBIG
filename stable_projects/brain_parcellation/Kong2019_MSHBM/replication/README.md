# Replication of Multi-session hierarchical Bayesian model (MS-HBM)

The replication of MS-HBM contain several tasks:
1. The group priors estimation on two sets of data:
   - GSP data in fsaverage5 surface space: 37 subjects, each subject has 2 sessions
   - HCP data in fs_LR_32k surface space: 40 subjects, each subject has 4 sessions
2. The individual-level parcellation generation on two sets of data:
   - HNU data in fsaverage5 surface space: 30 subjects, each subject has 10 sessions
   - HCP data in fs_LR_32k surface space: 755 subjects, each subject has 4 sessions
   
Notice that all filenames and directories in this replication **work for CBIG lab only**.

----

References
==========
+ Kong R, Li J, Orban C, et al. [Spatial Topography of Individual-Specific Cortical Networks Predicts Human Cognition, Personality, and Emotion](https://academic.oup.com/cercor/article/29/6/2533/5033556). Cerebral Cortex, 29(6):2533-2551, 2019

----

Data
====
In this replication, we will perform the algorithms on the functional connectivity profiles of 3 datasets:
+ **GSP:** `$CBIG_MSHBM_REP_GSP_DIR`

+ **HNU:** `$CBIG_MSHBM_REP_HNU_DIR`
  
+ **HCP:** `$CBIG_MSHBM_REP_HCP_DIR/??????/MNINonLinear/Results/rfMRI_REST?_??/postprocessing/MSM_reg_wbsgrayordinatecortex`

----

Run
====
### Group priors estimation
In terminal, call the replication wrapper script:
```
$CBIG_CODE_DIR/stable_projects/brain_parcellation/Kong2019_MSHBM/replication/CBIG_MSHBM_replication_wrapper.sh <output_dir>
```
In this script, it will submit two jobs to `circ_spool`, one for estimating group priors of 17 networks for 37 GSP subjects (each subject has 2 sessions), one for estimating group priors of 17 networks for 40 HCP subjects (each subject has 4 sessions). Each job will require 120 GB and 16 CPU cores. They will be finished in around 10 hours.

Note that the functional connectivity profiles and the initialization parameters from the Yeo2011 group clustering are already generated. There is no need to re-generate the profiles. 

After running the above script, under `<output_dir>`:
+ `<output_dir>/estimate_group_priors/GSP_HNU/group/group.mat`
+ `<output_dir>/estimate_group_priors/HCP/group/group.mat`

These files contain the **pre-computed** initialization parameters from group-level 17-network clustering generated by Yeo et al., 2011.

+ `<output_dir>/estimate_group_priors/GSP_HNU/profile_list/training_set/lh_sess?.txt`
+ `<output_dir>/estimate_group_priors/GSP_HNU/profile_list/training_set/rh_sess?.txt`

These files contain functional connectivity profiles of 37 GSP subjects with 2 sessions. These files are also **pre-generated**.

+ `<output_dir>/estimate_group_priors/HCP/profile_list/training_set/sess?.txt`

These files contain functional connectivity profiles of 40 HCP subjects with 4 sessions. These files are also **pre-generated**.

The estimated group priors will be saved into `priors` folder, the final results will be saved as `Params_Final.mat`, which contains a struct variable `Params`.

+ `<output_dir>/estimate_group_priors/GSP_HNU/priors`
+ `<output_dir>/estimate_group_priors/HCP/priors`

The estimated group priors include:
1) Inter-subject functional connectivity variability -- `Params.epsil`
2) Group-level connectivity profiles for each network -- `Params.mu`
3) Intra-subject functional connectivity variability -- `Params.sigma`
4) Spatial prior which denotes the probability of each network occurring at each location -- `Params.theta`

### Individual-level parcellations generation
As **Group priors estimation** will take some time to run, the user can run the replication of individual-level parcellations generation without waiting for the two jobs finished.

The estimation of individual parcellation is very fast (~5-10 min), the user can run following command in Matlab instead of submitting jobs:

##### HNU data
```
cd(fullfile(getenv('CBIG_CODE_DIR'),'stable_projects','brain_parcellation','Kong2019_MSHBM','step3_generate_ind_parcellations'))

project_dir = '<output_dir>/generate_individual_parcellations/GSP_HNU';
subject = '1';
[lh_labels, rh_labels] = CBIG_MSHBM_generate_individual_parcellation(project_dir,'fsaverage5','5','17',subject,'100','50');
```

##### HCP data
```
cd(fullfile(getenv('CBIG_CODE_DIR'),'stable_projects','brain_parcellation','Kong2019_MSHBM','step3_generate_ind_parcellations'))

project_dir = '<output_dir>/generate_individual_parcellations/HCP'
subject = '1';
[lh_labels, rh_labels] = CBIG_MSHBM_generate_individual_parcellation(project_dir,'fs_LR_32k','4','17',subject,'100','30');
```

If the user want to test for more subjects, please change `subject = 1` to other number.

The generated individual parcellations will be saved under:
+ `<output_dir>/generate_individual_parcellations/GSP_HNU/ind_parcellation`
+ `<output_dir>/generate_individual_parcellations/HCP/ind_parcellation`


----

Check Results
====

### Group priors estimation
The user can compare 

+ `<output_dir>/estimate_group_priors/GSP_HNU/priors/Params_Final.mat`
+ `<output_dir>/estimate_group_priors/HCP/priors/Params_Final.mat`

with

+ `$CBIG_CODE_DIR/stable_projects/brain_parcellation/Kong2019_MSHBM/replication/results/GSP_HNU/priors/Params_Final.mat`
+ `$CBIG_CODE_DIR/stable_projects/brain_parcellation/Kong2019_MSHBM/replication/results/HCP/priors/Params_Final.mat`

### Individual-level parcellations generation
The user can compare

+ `<output_dir>/generate_individual_parcellations/GSP_HNU/ind_parcellation/test_set/Ind_parcellation_MSHBM_sub1_w100_MRF50.mat`
+ `<output_dir>/generate_individual_parcellations/HCP/ind_parcellation/test_set/Ind_parcellation_MSHBM_sub1_w100_MRF30.mat`

with

+ `$CBIG_CODE_DIR/stable_projects/brain_parcellation/Kong2019_MSHBM/replication/results/GSP_HNU/ind_parcellation/Ind_parcellation_MSHBM_sub1_w100_MRF50.mat`
+ `$CBIG_CODE_DIR/stable_projects/brain_parcellation/Kong2019_MSHBM/replication/results/HCP/ind_parcellation/Ind_parcellation_MSHBM_sub1_w100_MRF30.mat`


----

Bugs and Questions
====
Please contact Ru(by) Kong at roo.cone@gmail.com.

