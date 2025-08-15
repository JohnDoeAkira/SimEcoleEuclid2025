# Prerequisites

## Le CC-IN2P3

### Login

Nous utiliserons le SDC francais (Science data center CC-IN2P3) pour la session Hands on. C'est un centre de calcul 
de Lyon opere par l'In2p3 (CNRS pour la physique des particules). Vous avez recu des login/password temporaires pour vous 
connecter au CC. Voici comment s'y connecter par ssh :

    ssh -Y <user>@cca.in2p3.fr

### Ressources 

Le CC a reserve deux serveurs (de 64 coeurs / 192Go de RAM). Cela suffira pour une douzaine de coeurs 
par utilisateur. Concernant l'espace disque, nous travaillerons sur /sps/training. L'espace disque ne devrait 
pas poser de probleme.

### Interactive jobs

Lorsque vous vous connectez, vous arrivez sur une noeud de login qui ne doit pas etre utilise pour des calculs intensifs. Pour effectuer des calculs, il faut soit utiliser le systeme de distribution de jobs (slurm batch schuduling) ou un job interactif.

Les jobs interactifs permettent de se connecter avec une interface en ligne de commande sur des noeuds de calcul. Attention, les jobs interactifs sont distribues sur la queue generale et non pas sur les serveurs reserves.

Voici un exemple d'une requete pour un job interactif de 3 coeurs pour un walltime de 3 heures (en bash).

    srun --account=training --reservation=training_67 -n 3 --time=3:00:00 --pty bash -i

### Software additionnels

Installer topcat sur votre machine personnelle : 

    https://www.star.bris.ac.uk/~mbt/topcat/

### Documentation

Pour etre plus familier de l'usage du CC dans Euclid :

    https://cceuclid-doc.in2p3.fr/


## Les repertoires de travail

Les repertoires de travail sont deja crees avec le chemin suivant : 


    /sps/training/<UserName>

Pour lancer l'environnement software EDEN, executer : 

    export User_area=/sps/training/<UserName>/Projects/
    source /cvmfs/euclid-dev.in2p3.fr/EDEN-3.1/bin/activate


## Le pipeline SIM 

Pour la production automatique, les versions successives des codes du segment sol Euclid sont compile, teste et deployes de maniere automatique sur les centre de calcul (sous forme de systeme de fichier montes a distance cvmfs). Nous utiliserons ces versions directement accessibles sans necessite d'installation specifique.

Si vous souhaitez modifier les briques du pipeline pour les executer avec vos modifications, c'est toujours possible en suivant les sections suivantes.

Nous allons tout de meme recuperer le code du pipeline pour en identifier les composantes.

### Get the SIM pipeline repository

Pour ceux qui ont acces au GitLab Euclid, clonez le repo localement :

    mkdir /sps/training/<UserName>/Projects
	cd /sps/training/<UserName>/Projects
	git clone https://gitlab.euclid-sgs.uk/PF-SIM/sim_ial_pipelines.git sim_ial_pipeline
	git checkout 3.3.5

Pour les autres, copiez le depuis : /sps/training/reference/Projects/

    mkdir /sps/training/<UserName>/Projects
	cd /sps/training/<UserName>/Projects
    cp -r /sps/training/reference/Projects/sim_ial_pipeline/ .

Regardez les trois fichiers suivants : 
* *SIM_IAL_Pipelines/auxdir/SIM_Pipelines/PkgDef_SIM.py* : description des Processing Elements (briques) du pipeline
* *SIM_IAL_Pipelines/auxdir/SIM_Pipelines/SIM_TUPipeline/PipScript_SIM_TU.py* : Chainage sequentiel (et parallele) des briques du pipeline TU
* *SIM_IAL_Pipelines/auxdir/SIM_Pipelines/SIM_VIS_PostTUPipeline/PipScript_SIM_VIS_PostTU.py* : Chainage sequentiel (et parallele) des briques du pipeline TU

Identifier les divers elements du PkgDef : SimPlanner, EuclidTU, Sim<VIS-NIP-NIS-EXT>Splitter, SIM<VIS-NIP-NIS-EXT>Detector
Regardez le chainage dans les PipScripts

## Recuperer les donnees pour la simulation

Pour cela, copier l'integralite du repertoire /sps/training/reference/COSMOS_VIS dans votre repertoire de travail. 

Attention, si vous le pouvez ne copiez pas le contenu du repertoire data dans votre repertoire. Creez plutot un repertoire workdir/data (dans votre repertoire workdir/) et liez les elements de data dans celui ci. Cela permet d'eviter de dupliquer inutilement les elements communs a tous les utilisateurs.

    mkdir /sps/training/<UserName>/COSMOS_VIS/workdir/data
    cd /sps/training/<UserName>/COSMOS_VIS/workdir/data
    ln -s /sps/training/reference/COSMOS_VIS/workdir/data/* .

	
The final directory tree is :


Le repertoire data n'est pas liste (trop gros). Il contient (entre autres fichiers) les elements references dans la base de donnees, les catalogues FITS et les fichiers references dans les calproducts.

Essayez d'identifier les elements de la simulations : 
* les catalogues d'entree (etoiles et galaxies)
* la base de donnee mission (MBD)
* la sim request
* les fichiers de survey
* les calproducts

Si vous avez installe topcat (https://www.star.bris.ac.uk/~mbt/topcat/), copiez un catalogue d'etoiles sur votre machine locale et afficher la distribution spatiale des objets (ra-dec) et la distribution en magnitude dans la bande VIS (-2.5log10(TU_FNU_VIS/3631)).


