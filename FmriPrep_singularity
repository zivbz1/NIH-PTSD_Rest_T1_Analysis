#!/bin/bash
#
# The fMRIPrep script is meant to streamline the preprocessing of fMRI dataset.
# The input is a bids dataset 
# The output is preprocessed data set ready for first level analysis. 
# The script can be run on the complete dataset or every # subjects. Make sure all subjects are ran on the same fMRIPrep version.
# This is a bash script and should be ran from the HPC terminal (HPC desktop/shell/VScode)
# This script uses full path, hence, you do not have to be in a spesific directory to run it. 
# Tip, create a new folder and run the script from it as the script produce (2*number of subject) log files
#
# Inorder for fMRIPrep to work cleanly without the need to create/load conda enviroment it is packed into a containr named singularity
# The singularity is packed in an img file (no need to load a singulrity module).
# This script loads the fMRIprep img file and automatically runs all the preprocessing phases on the bids compatible dataset.
# In order to run it, you will have to validate the dataset using
# http://bids-standard.github.io/bids-validator/
# or add the --skip-bids-validation decoration. 
#
# Once your dataset is ready (bids validated/decided to ignore) 
# 1. change the name of the job (what ever you feel like) on line 25
# 2. Change the number of participants on line 26
# 3. add a list of subjects
# 4. adjust directories path
# 5. From the HPC terminal run using the command "sbatch FmriPrep_singularity.sh"
#
#SBATCH --j Rest-test3 # job name
#SBATCH --array=1-20 # number of participants as range starting at 1 (i.e., for 5 participants: 1-5)
#SBATCH --time=48:00:00 # HPC will give you this amount of time to run the process. This is usually enough time
#SBATCH -n 1 # how many nodes you are asking. This is running each subject on a differnt node so 1 is enough
#SBATCH --cpus-per-task=4 # How many CPUs. This is enough cpus no need to change
#SBATCH --mem-per-cpu=10G # How much memory per CPU. This is enough memory no need to change

# resouce you are using are nodes * CPUs * memory - if you go above 120 per subject you will have to wait a lot of time to get an opening
# Outputs ----------------------------------
#SBATCH -o %x-%A-%a.out # this will give you the list of commands and there results (success/failure). If the run fails here you will get the spesifcs
#SBATCH -e %x-%A-%a.err # this will give you a short file with what errors were during the execution
#SBATCH --mail-user=nachshon.korem@yale.edu # replace with your email
#SBATCH --mail-type=ALL
# ------------------------------------------

# enter subject list with only space between them and the "sub-" prefix (i.e. sub-10 sub-11)
# SUBJ=(sub-13)

SUBJ=(sub-0005 sub-0034 sub-0081 sub-0193 sub-0216 sub-0350 sub-0365 sub-0366 sub-0435 sub-0454 sub-0497 sub-0539 sub-0545
      sub-0643 sub-0678 sub-0682 sub-0687 sub-0692 sub-0695 sub-0708 sub-0727 sub-0758 sub-0789 sub-0790 sub-0839 sub-0862
      sub-0871 sub-0875 sub-0892 sub-0939 sub-1010 sub-1016 sub-1038 sub-1060 sub-1062 sub-1127 sub-1148 sub-1159 sub-1163
      sub-1216 sub-1238 sub-1255 sub-1306 sub-1316 sub-1319 sub-1331 sub-1333 sub-1343 sub-1350 sub-1361 sub-1393 sub-1428
      sub-1430 sub-1460 sub-1470 sub-1482 sub-1498 sub-1513 sub-1519 sub-1542 sub-1551 sub-1565 sub-1619 sub-1632 sub-1651
      sub-1716 sub-1718 sub-1749 sub-1750 sub-1766 sub-1775 sub-1800 sub-1803 sub-1840 sub-1860 sub-1887 sub-1888 sub-1904
      sub-1909 sub-1936 sub-1954 sub-1957 sub-1959 sub-1990 sub-2010 sub-2019 sub-2025 sub-2027 sub-2028 sub-2032 sub-2037
      sub-2064 sub-2070 sub-2099 sub-2105 sub-2108 sub-2113 sub-2124 sub-2142 sub-2155 sub-2164 sub-2167 sub-2169 sub-2196
      sub-2199 sub-2228 sub-2236 sub-2267 sub-2288 sub-2326 sub-2333 sub-2377 sub-2392 sub-2393 sub-2405 sub-2417 sub-2423
      sub-2468 sub-2492 sub-2512 sub-2519 sub-2520 sub-2527 sub-2530 sub-2544 sub-2590 sub-2608 sub-2645 sub-2646 sub-2680
      sub-2692 sub-2701 sub-2714 sub-2759 sub-2772 sub-2795 sub-2823 sub-2828 sub-2845 sub-2846 sub-2896 sub-2920 sub-2942
      sub-2958 sub-3025 sub-3029 sub-3056 sub-3121 sub-3128 sub-3135 sub-3143 sub-3146 sub-3148 sub-3191 sub-3256 sub-3259
      sub-3299 sub-3315 sub-3319 sub-3354 sub-3408 sub-3447 sub-3533 sub-3541 sub-3586 sub-3610 sub-3612 sub-3625 sub-3646
      sub-3659 sub-3671)

SUBJ=(sub-0365 sub-0366 sub-0545 sub-0695 sub-0708 sub-0727 sub-0789 sub-1428 sub-1460 sub-1887 
      sub-1957 sub-1959 sub-2025 sub-2105 sub-2199 sub-2228 sub-2527 sub-2942 sub-3319 sub-3447)

# adjust directories path based on the bids directories
BASE_DIR="/gpfs/gibbs/pi/levy_ifat/Nachshon/Aging" # not really used, not sure why is it here
BIDS_DIR="/gpfs/gibbs/pi/levy_ifat/Nachshon/BIDS/new" # Location of the bids folder.
DERIVS_DIR="derivatives" # the derivatives folder should be inside the bids folder.
WORK_DIR="/home/nk549/scratch60/work" # enter working directory here - preferably on scratch60.
# If you want to rerun fMRIprep clean the working directory before

mkdir -p $HOME/.cache/templateflow
mkdir -p ${BIDS_DIR}/${DERIVS_DIR}
mkdir -p ${BIDS_DIR}/${DERIVS_DIR}/freesurfer-6.0.1
ln -s    ${BIDS_DIR}/${DERIVS_DIR}/freesurfer-6.0.1 ${BIDS_DIR}/${DERIVS_DIR}/freesurfer

# this is loading the license to run freesurfer
export SINGULARITYENV_FS_LICENSE=$HOME/pipeline/licenseFreeSurfer.txt # freesurfer license file
export FS_LICENSE=$HOME/pipeline/licenseFreeSurfer.txt # freesurfer license file

# this create a folder for fMRIprep. If fMRIprep fails to run clean this folder 
# cd ~/.cache/templateflow/
# rm *
export SINGULARITYENV_TEMPLATEFLOW_HOME="/templateflow"

# Load the fMRIprep img
#SINGULARITY_CMD="singularity run --cleanenv -B $HOME/.cache/templateflow:/templateflow -B ${WORK_DIR}:/work /gpfs/gibbs/pi/levy_ifat/shared/fmriPrep/fmriprep-21.0.1.simg"
SINGULARITY_CMD="singularity run --cleanenv -B $HOME/.cache/templateflow:/templateflow -B ${WORK_DIR}:/work /gpfs/gibbs/pi/levy_ifat/shared/fmriprep-1.2.2.simg"


# this is where the magic starts
echo Starting ${SUBJ[$SLURM_ARRAY_TASK_ID-1]}

# this is the line that runs the code. If you want to use --skip-bids-validation, enter it as part of the next line (before the --output-space decoration).
#cmd="${SINGULARITY_CMD} ${BIDS_DIR} ${BIDS_DIR}/${DERIVS_DIR} participant --participant-label ${SUBJ[$SLURM_ARRAY_TASK_ID-1]} -w /work/ -vv --omp-nthreads 8 --nthreads 12 --mem_mb 30000 --output-spaces MNI152NLin2009cAsym:res-2 anat fsnative fsaverage5 --cifti-output"
      # --use-aroma"# 
cmd="${SINGULARITY_CMD} ${BIDS_DIR} ${BIDS_DIR}/${DERIVS_DIR} participant --participant_label ${SUBJ[$SLURM_ARRAY_TASK_ID-1]}  -w /work/ --omp-nthreads 4 --nthreads 4 --use-aroma --aroma-melodic-dimensionality -100 --write-graph"
# --cifti-output" --fs-no-reconall


# Setup done, run the command
echo Running task ${SLURM_ARRAY_TASK_ID}
echo Commandline: $cmd
eval $cmd
exitcode=$?

# Output results to a table
echo "sub-$subject   ${SLURM_ARRAY_TASK_ID}    $exitcode" \
      >> ${SLURM_JOB_NAME}.${SLURM_ARRAY_JOB_ID}.tsv
echo Finished tasks ${SLURM_ARRAY_TASK_ID} with exit code $exitcode
exit $exitcode
