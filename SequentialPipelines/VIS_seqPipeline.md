# VIS simulation pipeline - ELViS

## Structure du pipeline

### Inputs

Tous les inputs sont stockes dans le workdir. En production automatique, le workdir est cree et rempli par l'IAL a partir du PPO (Pipeline Processing Order). Pour un run en local (pipeline-runner), la liste des entrees est contenue dans le fichier wordir/params.dat

Simulation request pour le premier pipeline (TU pipeline)

    workdir=/sps/training/reference/COSMOS_VIS/workdir/
    logdir=logs
    simrequest=sim_request_VIS_DR1_Step2_COSMOS_4924_R0.xml galcat=galcat.json
    starcat=starcat.json
    mdb=EUC_MDB_MISSIONCONFIGURATION_DEV_2025-02-04-1.xml
    survey=OSS_RSD_20240214T000000-20241231T233933_024.xml.xml
    orbit=OEM__EFDS_000V0_______________13266-13310________________00033.EUC_38fa9afa-4343-4d86-8a3b-3de9db1965d9.xml
    pkgRepository=/cvmfs/euclid-dev.in2p3.fr/EDEN-3.1/opt/euclid/SIM_IAL_Pipelines/3.3.5/InstallArea/x86_64-conda_ry9-gcc11-o2
    g/auxdir/SIM_Pipelines/
    pipelineDir=/cvmfs/euclid-dev.in2p3.fr/EDEN-3.1/opt/euclid/SIM_IAL_Pipelines/3.3.5/InstallArea/x86_64-conda_ry9-gcc11-o2g/
    auxdir/SIM_Pipelines/

* **workdir** : Running location
* **logdir** : log directory
* **Simulation Request** : XML file
* **starcat** : json list of the star catalog
* **galcat** : json list of cataloxy catalog
* **Mission Database** : XML file + associated files in the data directory
* **survey** : Survey file
* **orbit** : Orbit file (satellite speed & location)
- **pkgRepository** and **pipelineDir** : IAL pipeline path

Simulation request pour le second pipeline (postTU Instrument pipeline)

    workdir=/sps/training/reference/COSMOS_VIS/workdir/
    logdir=logs
    simrequest=sim_request_VIS_DR1_Step2_COSMOS_4924_R0_PsfLUT.xml
    tucatalog=SimTU_pkg/outputTU.xml
    inputconfs=VIS_inputconf.json
    mdb=EUC_MDB_MISSIONCONFIGURATION_DEV_2025-02-04-1.xml
    psf=sim_LUT_FLIGHTPDC_INFOCUS_R2_20240710T120000.xml
    bias=VIS_MasterBias_EUCLID_1.0.0-ON_THE_FLY-pcasenov-PLAN-000000-1AR2V4HP-20230815-071343-0-dpdcalib_data-0.xml
    prnu=VIS_SmallScaleFlat_EUCLID_1.0.3-ON_THE_FLY-slancien-PLAN-000001-KFHE9UY6-20231201-190014-0-smallscaleflat_out-0.xml
    flagmaps=flagmaps.json
    gain=VIS_GainCalibration_EUCLID_1.0.2-ON_THE_FLY-slancien-PLAN-000001-TUBANE8M-20231017-142749-0-gain_model_xml_out-0.xml
    distortion=VIS_AstrometryCalibration_EUCLID_1.0.2-ON_THE_FLY-pcasenov-PLAN-000000-RZMXTW96-20230823-110940-0-dist_model_xm
    l_out-0.xml
    ghost=VIS_GhostsCalibration_EUCLID_1.0.3-ON_THE_FLY-pcasenov-PLAN-000000-9EQW8ZVP-20231020-075041-0-ghost_model_xml_out-0.
    xml
    nonlinearity=DpdVisNonLinearityModel_TVAC2022_Patty_FlatfieldNL_json-EUCLID.xml
    xtalk=VIS_XTalkCalibration_EUCLID_1.0.2-ON_THE_FLY-pcasenov-PLAN-000000-1S0O0JUF-20231020-144555-0-xtalk_model_xml_out-0.x
    ml
    zerop=VIS_PhotometryCalibration_EUCLID_1.0.2-ON_THE_FLY-pcasenov-PLAN-000000-USCUC40M-20231107-221406-0-zero_point_xml_out
    -0.xml
    bfe=VIS_BrighterFatterCalibration_EUCLID_1.0.3-ON_THE_FLY-pcasenov-PLAN-000000-OOOC9BKK-20231016-095630-0-bfecalib_xml_out
    -0.xml
    illumination=illumination.json
    QCTable=VISQC_v3.0_June24June25.xml
    TK_Zplugin=le2.json
    cosmicRay=CRlib.json
    svmstraylight=svmstraylight.json
    pkgRepository=/cvmfs/euclid.in2p3.fr/EDEN-3.1/opt/euclid/SIM_IAL_Pipelines/3.3.5/InstallArea/x86_64-conda_ry9-gcc11-o2g/auxdir/SIM_Pipelines/
    pipelineDir=/cvmfs/euclid.in2p3.fr/EDEN-3.1/opt/euclid/SIM_IAL_Pipelines/3.3.5/InstallArea/x86_64-conda_ry9-gcc11-o2g/auxdir/SIM_Pipelines/

* **tucatalog** : Catalogues d'etoiles et de galaxies 
* **inputconfs** : liste json de fichier de configuration d'entree (VIS task)
* **Instrument models** : divers formats de fichiers de modeles d'instrument

### Processing elements (briques du pipeline)

En production VIS, les pipelines (TU et postTU) sont executes par le pipelineRunner qui suit les instructions des fichiers PipScript des repertoires de pipeline : 

* /cvmfs/euclid.in2p3.fr/EDEN-3.1/opt/euclid/SIM_IAL_Pipelines/3.3.5/InstallArea/x86_64-conda_ry9-gcc11-o2g/auxdir/SIM_Pipelines/SIM_TUPipeline/PipScript_SIM_TU.py
* /cvmfs/euclid.in2p3.fr/EDEN-3.1/opt/euclid/SIM_IAL_Pipelines/3.3.5/InstallArea/x86_64-conda_ry9-gcc11-o2g/auxdir/SIM_Pipelines/SIM_VIS_PostTUPipeline/PipScript_SIM_VIS_PostTU.py

Ici nous allons tout executer manuellement.
La seule difference est l'automation par l'IAL.
* SimPlanner : Separe la requete par observation 
* EuclidTU : Merge les catalogues
* SimTuInputConfMatch : Interface pipeline1 - pipeline2
* SimPlannerPointingProgramm : Separe par instrument 
* EuclidVisSplit : Separe les simulations par detecteur (et construit les fichiers de configuration pour les simulateurs d'instrument)
* EuclidVisDetector : Execute la simulation
* EuclidVisCombine : Combine les detecteurs (36 CCD pour VIS) 

Les scripts correspondant sont les suivants pour le pipeline TU (/sps/training/reference/VIS/workdir): 
* 0_SimPlanner.py
* 1_EuclidTU.py

Les scripts correspondant sont les suivants pour le pipeline postTU (/sps/training/reference/VIS/workdir): 
* 2_SimTuInputConfMatch.py
* 3_SimPlannerPointing
* 4_EuclidVisSplit.py
* 5_EuclidVisDetector.py

Essayez de retrouver a quels scripts correspondent les fonctions des pipeline Script ci-dessus (PipScript_TU.py et PipScript_VIS_PostTU.py).

# Executer le pipeline

Copier les helper scripts des PE (Processing Elements) dans votre repertoire workdir (+ simrequest):

    cp /sps/training/reference/COSMOS_VIS/workdir/?_*py .
    cp /sps/training/refernce/COSMOS_VIS/workdir/sim_request_VIS_DR1_Step2_COSMOS_4924_R0_PsfLUT.xml .

Lire les scripts avant (ou pendant) l'execution pour comprendre leurs interfaces (input/ output).

### Mode interactif

Si vous lancer les executables en mode interactif (ligne de commande), il est imperatif de vous placer dans un job interactif sur un noeud de calcul et de ne pas rester sur le noeud de login :

    srun --account=training --reservation=training_67 -n 3 --time=3:00:00 --pty bash -i
    

### job batch

Si vous voule



## TU pipeline

### Etape 0 : SimPlanner

    python3 0_SimPlanner.py >& SimPlanner.log
 
Quels fichiers / repertoires ont ete crees ? que contiennent ils ?

Creates SimPlanner_pkg/pointing_input_files_list.json : the list of simulated pointings (one here)
* contains data/EUC_SIM_OUTPUT-EUCLID-018539_0A0369F9BC4B-0027892_20220613T221335.145257Z_VIS_C13_PILOT_R3.xml
  * contain the list of TASKS 
    * these tasks as xml files are stored in the data directory
    



## Etape 1 : True Universe

Fusionner les catalogues d'entree :

    python3 2_EuclidTU.py >& EuclidTU.log

Quels fichiers / repertoires ont ete crees ? que contiennent ils ?
Afficher (topcat) la distribution statiales des objets du catalogue de sortie. Afficher le comptage en magnitude des objets.

Creates the output TU object :
* call_pointing.iterations.1.SimTU_pkg/outputTU.xml
    * create the TU catalogs (in data directory)
      * EUC_SIM_TUSTARCAT-18539_0A0369F9BC4B-0024553_20220613T225153.418788Z_v13_SC8_MAIN.fits
      * EUC_SIM_TUGALCAT-18539_0A0369F9BC4B-0024553_20220613T225155.306234Z_v3_SC8_Pilot.fits

## postTU pipeline

### Etape 2 : SimTuInputConfMatch

    python3 2_SimTuInputConfMatch.py > SimTuInputConfMatch.log

Script d'interface. Modification des fichier de configuration

### Etape 3 : SimPlannerPointingProgramm

    python3 3_SimPlannerProgram.py >& SimPlannerPointingProgramm.log

Quels fichiers sont crees ?

Creates call_pointing.iterations.1.SimSplitter_pkg/VIS_input_pointing_list.json
* contains the list of VIS_Tasks


## Step 4 : Euclid Vis Splitter

Separation des jobs par detecteur, preparation des fichiers de configuration.

    python3 4_EuclidVisSplit.py >& EuclidVisSplit.log

Quels fichiers / repertoires ont ete crees ? 

Creates the ELViS config files for all detectors (myVISconf***)
* split the model files from FPA to CCD individal files (HOTPIX, BIAS, etc ...)
* split catalog files in CCDs

## Step 5 : execute le simulateur d'image par detecteur 

Execute la simulation. 

WARNING : runtime ~ 15mn ! Etudier les logs (EuclidVisDetector.log)

    python3 5_EuclidVisDetector.py >& EuclidVisDetector.log

Ou sont les sorties ? de quels types sont elles ?
Ouvrir les images avec ds9 (apres copie des fichiers FITS sur votre machine locale).

## Step 5 bis : tuner le fichier de configuration de ELViS pour sauvegarder les images intermediaires

Ajouter ces parametres au fichier de configuration de ELViS :

    "FITS_STARS": 1         ,
    "FITS_GHOSTS": 1         ,
    "FITS_GALAXIES": 1         ,
    "FITS_FAINTGALAXIES": 1         ,
    "FITS_CALIBLAMP": 1         ,
    "FITS_SHUTTER": 1         ,
    "FITS_IPQE": 1         ,
    "FITS_THERMAL": 1         ,
    "FITS_BACKGROUND": 1         ,
    "FITS_FLATFIELD": 1         ,
    "FITS_CHARGEINJECTION": 1         ,
    "FITS_CTI_CHARGEINJECTION": 1         ,
    "FITS_TRAP_PUMPING": 1         ,
    "FITS_COSMICRAYS": 1         ,
    "FITS_DARK": 1         ,
    "FITS_BLEEDING": 1         ,
    "FITS_NOISE": 1         ,
    "FITS_COSMETICS": 1         ,
    "FITS_PREOVERSCAN": 1         ,
    "FITS_PARALLELOVERSCAN": 1         ,
    "FITS_RADIATIONDAMAGE": 1         ,
    "FITS_NONLINEARITY": 1         ,
    "FITS_READOUTNOISE": 1         ,
    "FITS_ELECTRON2ADU": 1         ,
    "FITS_BIAS": 1         ,
    "FITS_XTALK": 1         ,
    "FITS_DISCRETIZE": 1         ,
    "FITS_BFE": 1

Le fichier de config est le dernier fichier de la forme myVISconf_CCD35*.txt du repertoire data.

Ensuite executer a nouveau l'etape 5 


    python3 5_EuclidVisDetector.py >& EuclidVisDetector.log


