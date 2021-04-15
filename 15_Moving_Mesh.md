# Moving Mesh
Netz generieren anschlißend boundarys anpassen:

#### ./constant/polyMesh/boundary 
Flächen die Parameter des Interfaces sind müssen als *cyclicAMI* definiert
werden:

```cpp
AMI_SATOR_ROTOR_SIDE-1
{
    type            cyclicAMI;
    nFaces          <n>;
    startFace       <n>;
    neighbourPatch  AMI_SATOR_ROTOR_SIDE-2;
    transform       noOrdering;
}
```

#### ./constant/MRFProperties

In dieser Datei wird der Frozen Rotor definiert
* rotierende Fluidzone
* Koordinatenursprung
* Rotationsachse
* Drehzahl in rad/s
* ruhende Flächen
```bash
MRF1
{
    cellZone    rotor;
    active      yes;

    nonRotatingPatches (ROTOR_SURFACE);

    origin    (0 0 0);
    axis      (0 0 1);
    omega     6.2832;
}
```

#### ./system/topoSetDict
Definition der Fluidzone
```bash
cp $FOAM_UTILITIES/mesh/manipulation/topoSet/topoSetDict system/
```
Anpassen, anschließend:
```bash
topoSet
```
Anschließend in Zonen umwandeln
```bash
setsToZones -noFlipMap
```
Anschließend übliche Modellbildung

## Transiente Berechnung mit pimpleFoam

#### ./constant/polyMesh/boundary

```cpp
AMI_SATOR_ROTOR_SIDE-1
{
    type            cyclicAMI;
    nFaces          <n>;
    startFace       <n>;
    neighbourPatch  AMI_SATOR_ROTOR_SIDE-2;
    transform       noOrdering;
}
```

#### ./constant/dynamicMeshDict
Steuert die Rotationsbewegung
* Definition der Methode, z.B. *dynamicMotionSolverFvMesh*
* wewlche cellZone dynamisch sein soll
* welche funktion der verwendeten Methode verwendet wird z.B. *rotatingMotion*
* Koeffizienten der jeweiligen Methode

```bash
dynamicFvMesh   dynamicMotionSolverFvMesh;

motionSolver    solidBody;

cellZone        rotor;

solidBodyMotionFunction  rotatingMotion;

origin        (0 0 0);
axis          (0 0 1);
omega         6.2832;
```

checken ob Fluidzone bereits existiert
```bash
grep rotor ./constant/polyMesh/*
```
Falls existiert -> überspringen sonst:
```bash
cp $FOAM_UTILITIES/mesh/manipulation/topoSet/topoSetDict system/
#anpassen -> dann:
topoSet
setsToZones -noFlipMap
```
-> Weitere Modellbildung

#### Initialisierung des Problems
Initialisierung kann durch stationäre Startlösung ergänzt werden ->
Interpolation auf das aktuelle Rechennetz
```bash
mapFields -consistent -sourceTime 500 ../050_RuehrerStat
```


