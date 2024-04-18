# HIV-proteinase-binding

## Steps

### Preparation of the molecules

I split the molecule into two files `saquinavir.pdb` (the ligand) and `hiv1.pdb` (the hvi-1 proteinase).

I foudnd a `You clicked /obj01//A/ROC100/O2A`

```
HETATM 1559  O2 AROC A 100    21.877  -0.510  11.108  0.56 11.94       O
HETATM 1560  O2 BROC A 100    21.842   0.352  11.780  0.44 13.69       O
```

```
prepare_receptor -r hiv1.pdb -A hydrogens -o hiv1.pdbqt
prepare_ligand -l saquinavir.pdb -o saquinavir.pdbqt
```
### Vina

```
receptor = hiv1.pdbqt
ligand = saquinavir.pdbqt # commneted out when looping over fda molecules

center_x = 21.877
center_y = -0.510
center_z = 11.108

size_x = 20
size_y = 20
size_z = 20
```

### Force field

```
antechamber -i data/saquinavir_hydrogens.pdb -fi pdb -o data/saquinavir_hydrogens.mol2 -fo mol2 -c bcc -s 2 -nc 0 -m 1
parmchk2 -i data/saquinavir_hydrogens.mol2 -f mol2 -o data/saquinavir_hydrogens.frcmod
cp data/saquinavir_hydrogens.frcmod lig.frcmod
cp data/saquinavir_hydrogens.mol2 lig.mol2
tleap -f configs/tleaprc.ff14SB_lig
python scripts/parmed_amber2gmx.py lig
```

```
cp data/hiv1.pdb pro.pdb
tleap -f configs/tleaprc.ff14SB_protein
python scripts/parmed_amber2gmx.py pro
```

## Ideas

Test mutated proteinase to see if the inhibitor binds.

---


Ensure you are in MD conda environment.
1.	Ligand preparation
        -	Convert from .pdbqt to .pdb. Then rename file to `ligand.pdb`
        ```bash
        babel -ipdbqt fda_20_out.pdbqt -opdb -O fda_1_out.pdb -m -h
        mv fda_20_out1.pdb ligand.pdb
        antechamber -i ligand.pdb -fi pdb -o lig.mol2 -fo mol2 -c bcc -s 2 -nc 0 -m 1
       parmchk2 -i lig.mol2 -f mol2 -o lig.frcmod
       tleap -f tleaprc.ff14SB_lig
       python parmed_amber2gmx.py lig
        ```
        - `antechamber -i ligand.pdb -fi pdb -o lig.mol2 -fo mol2 -c bcc -s 2 -nc 0 -m 1`
        - `parmchk2 -i lig.mol2 -f mol2 -o lig.frcmod`
        - `tleap -f tleaprc.ff14SB_lig`
        - `python parmed_amber2gmx.py lig`
2.	Protein preparation. Ensure protein is not protonated. Name protein to pro.pdb.
        - tleap -f tleaprc.ff14SB_protein
        - python parmed_amber2gmx.py pro
