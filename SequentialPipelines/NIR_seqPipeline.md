# NIP simulation pipeline - Imagem

## Structure du pipeline

Voir [VIS pipeline](./VIS_seqPipeline.md). Structure identique, mais calproducts identiques.

### Inputs

Tous les inputs sont stockes dans le workdir. En production automatique, le workdir est cree et rempli par l'IAL a partir du PPO (Pipeline Processing Order). Pour un run en local (pipeline-runner), la liste des entrees est contenue dans le fichier wordir/params.dat

La simulation request pour le premier pipeline (TU pipeline) est identique a VIS.

Simulation request pour le second pipeline (postTU Instrument pipeline), calproducts specifiques :

    p_dark=NIR_MasterDark_EUCLID_2.0.2-CALIBRATION-glibet-PLAN-000000-1KKQAPCA-20241217-121112-0-outputs-0.xml
    p_nlmask=2123f817-d7e2-4ff9-b81e-f98557d46f6a.xml
    p_optmodel=NIR_DistortionModel_EUCLID_1.0.7-ON_THE_FLY-pcasenov-PLAN-000000-LWRXXORZ-20231020-113417-0-distortionmodel-0.xml
    ghostdichr=EUC_NIR_GHOST_DICHROIC_20231113.xml
    ghostfilt=EUC_NIR_GHOST_FILTER_20231113.xml
    p_persmask=NIR_PersistenceCheck_EUCLID_1.0.8-ON_THE_FLY-pcasenov-PLAN-000001-5DKCLRVM-20231004-200841-1-persistence_mask_output-0.xml
    p_DQC=DpdNIRDQC.xml
    p_smallflat=p_smallflat.json
    p_largeflat=p_largeflat.json

* **Instrument models** : fichiers de differents formats (metadata XML)


## Executer le pipeline

Copy the processing elements helper scripts in your workdir (+ SimRequest) : 

    cp /sps/training/reference/COSMOS_NIR/workdir/?_*py .
    cp /sps/training/reference/COSMOS_NIR/workdir/sim_request*xml .

Lire les scripts pour en comprendre les interfaces (similaires a VIS).

## TU pipeline

Cette etape est identique au pipeline VIS. 

## postTU pipeline



### Step 3 : SimPlannerPointingProgramm

    python3 1_SimPlannerPointingProgramm.py >& SimPlannerPointingProgramm.log

Quels fichiers sont crees ?

## Step 4 : Euclid NIP Splitter

separation en jobs par detecteurs 

    python3 3_EuclidNipSplit.py >& EuclidNipSplit.log

Quels fichiers sont crees ? Quels differences avec VIS ?

## Step 5 : Run the actual image simulation by detector 

Executer la simulation. 

WARNING : runtime ~ 15mn ! Lire les logs (tail EuclidNipDetector.log)

    python3 4_EuclidNipDetector.py >& EuclidNipDetector.log

Quels sont les sorties ? Visualiser les images avec ds9.

