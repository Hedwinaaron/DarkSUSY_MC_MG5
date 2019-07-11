# Private DarkSUSY MC in MG5
Note: The model implementation is now in the [model database](https://feynrules.irmp.ucl.ac.be/wiki/MSSMD) in FeynRules. Make sure to cite the reference before use in public.

## Create a directory and upload the package to the directory

    mkdir ~/MadGraph5
    cd ~/MadGraph5
    #cp /afs/cern.ch/cms/generators/www/MG5_aMC_v2.6.1.tar.gz ./
    #v2.4.2 is used in 2017 central MC
    cp /afs/cern.ch/cms/generators/www/MG5_aMC_v2.4.2.tar.gz ./
    #tar -xzf MG5_aMC_v2.6.1.tar.gz
    tar -xzf MG5_aMC_v2.4.2.tar.gz

## Get UFO model 
Go to the folder `MG5_aMC_vXXX/models`. Copy the UFO model there and unzip it to folder `MSSMD_UFO`:

    wget --no-check-certificate https://github.com/weishi10141993/DarkSUSY_MC_MG5/raw/master/MSSMDarkSector/MSSMD_UFO.tar.gz
    tar xavf MSSMD_UFO.tar.gz

Go to the folder `MSSMD_UFO` and do:

    python write_param_card.py

A `param_card.dat` will be generated. This will be used later for the decay in `madspin_card.dat`. You can manually change the mass of dark photon (3000022, MAD, default is 0.25 GeV), lightest neutalino (1000022, Mneu1, default is 10 GeV) and dark neutralino (3000001, MneuD, default is 1 GeV) in this `param_card.dat`.

## Set up ggH processes
Remove the default `proc_card.dat` in the `MG5_aMC_vXXX` directory and copy the following `proc_card.dat` there:
    
    wget https://raw.githubusercontent.com/weishi10141993/DarkSUSY_MC_MG5/master/MSSMDarkSector/proc_card.dat
    
Run `./bin/mg5_aMC proc_card.dat` and a folder called `MSSMD` will be generated. 

## Generate and decay particles

    cd /MadGraph5/MG5_aMC_vXXX/MSSMD/Cards
    
Copy the madspin card to the `Cards` directory:

    wget https://raw.githubusercontent.com/weishi10141993/DarkSUSY_MC_MG5/master/MSSMDarkSector/madspin_card.dat

Then go back to `MSSMD` folder and generate the events:

    cd ..
    ./bin/generate_events

Check the prompt shows `madspin = ON` and the `madspin_card.dat` is same as the one you just [copied](https://github.com/weishi10141993/DarkSUSY_MC_MG5/blob/master/MSSMDarkSector/madspin_card.dat).

Also you have the option to edit `run_card.dat` to change run settings, such as number of events, center of mass enegry, etc.

After you finish editing and saving the card, the event generation starts. (Note: it takes ~1hr to load the UFO model, so be patient. Current model implementation is built on MSSM model implementation. So it contains an ton of MSSM model parameters. If want to save time, further improvement could be done to slim the model to only contain relevant particles of the analysis.)

A `lhe.gz` file will be generated under `MG5_aMC_vXXX/MSSMD/Events/run_01_decayed_1` directory. Unzip the file to get the .lhe file:

    gunzip -d *.lhe.gz

## Change dark photon lifetime

    wget https://raw.githubusercontent.com/weishi10141993/DarkSUSY_MC_MG5/master/MSSMDarkSector/replace_lifetime_in_LHE.py
The lifetime can be changed in line 8 (default is 100 mm): `ctau_mean_mm = 100`
    
    python replace_lifetime_in_LHE.py > DarkSUSY_mH_125_mN1_10_mND_1_mGammaD_0p25_13TeV_cT_100_events80k.lhe

## LHE Validation [Optional]
The validation is a simple check of different distributions for the analysis. 

    wget https://raw.githubusercontent.com/weishi10141993/DarkSUSY_MC_MG5/master/MSSMDarkSector/LHE_read.py
    wget https://raw.githubusercontent.com/weishi10141993/DarkSUSY_MC_MG5/master/MSSMDarkSector/tdrStyle.py
    python LHE_read.py
