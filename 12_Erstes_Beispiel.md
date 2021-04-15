# Erstes Beispiel

# Rohrströmung mit simpleFoam berechnen (laminar)

## Neuen Case anlegen

```bash
# Neuer Ordner anlegen
cd $FOAM_RUN
mkdir 010_RohrstroemungLam
cd 010_RohrstroemungLam

# Basis Tutorial für den Case
cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/pitzDaily/* .
rm -rf constant/polyMesh

#Netzdateien kopieren
cp $FOAM_UEBUNGSFILES/01_Rohrstroemung/010_RohrstrLam.msh . 

#Netzgenerierung
fluent3DMeshToFoam 010_RohrstrLam.msh -scale 0.001
checkMesh
```
### Physikalische Modellgenerierung
* **./constant/turrbulenceProperties/** -> Wahl des Turbolenzmodell
* **./constant/transportProperties** -> Fluideigenschaften

### Mathematische Modellbildung
* **./system/controlDict** -> Rechendauer + Zeitschritt
* **./system/fvSchemes** -> ddtSchemes + gradSchemes + divSchemes +
    laplacianSchemes  + weitere Schemes zum Lösen der Gl
* **./system/fvSolution** -> Solver der einzelnen Variablen + Druck +
    Geschwindigkeitskopplung + Relaxations-Faktoren

### Initialisierung des Problems
-> Findet im Ordner **./0/** statt
* **./U/0** -> Startlösung der Geschwindigkeit
* **./U/p** -> Startlösung des Drucks

### Ausführung und Überwachung
```bash
simpleFoam
cp $FOAM_UEBUNGSFILES/01_Rohrstroemung/simpleFoamLam.slurm .
```

# Rohrströmung mit simpleFoam berechnen (turbolent)

## Case anlegen
```bash
mkdir 011_RohrstroemungKE
cd 011_RohrstroemungKE

cp -r ../010_RohrstroemungLam/* .
rm -rf constant/polyMesh
cp $FOAM_UEBUNGSFILES/01_Rohrstroemung/011_RohrstrTurbY30.msh .

fluent3DMeshToFoam 011_RohrstrTurbY30.msh  -scale 0.001
checkMesh
```
-> Solver Einstellungen analog, allerdings k omega nu initialisieren

# SST-Modell

# Parallelrechnung durchführen
###  Kopieren der decomposeParDict Datei (in den systems Ordner)
```bash
cp $FOAM_UTILITIES/parallelProcessing/decomposePar/decomposeParDict system/
```
### Anpassen der DecomposeParDict Datei (oberer Teil)
```cpp
numberOOfSubdomains     8;

methood                 scotch;
```
### Löschen aller nichtbenötigten Dateien!
Anschließend:
```bash
decompsePar
```
### Zusammensetzen des Rechengebiets nach der Rechnung
```bash
reconstructPar
```



