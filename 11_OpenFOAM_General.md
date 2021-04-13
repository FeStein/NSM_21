# Generelles zu OpenFoam

## Aufbau OpenFoam Case
* **Case**
    * **constant**
        * ...properties
        * **polyMesh**
            * points
            * cells
            * faces
            * boundary
    * **system**
        * controlDict
        * fvSchemes
        * fvSolution
    * **TimeStep**

## constant-Files

### transportProperties
* Steuert viskoses Verhalten (für inkompressible Medien!)
```cpp
transportModel  Newtonian;

nu              nu [0 2 -1 0 0 0 0] 1e-06; //Option 1
nu              1e-06; //Option 2
```

### turbulenceProperties
* legt fest, welche Kategorie der Turbolenzmodellierung verwendet werden soll
```cpp
simulationType RAS; //oder LES

RAS
{
    RASModel        kEpsilon;

    turbulence      on;

    printCoeffs     on;
}
```
* Standartinstallation in `$FOAM_SRC/turbolenceModels`

### ./constant/polyMesh/

* Beinhaltet alle Informationen über die Rechengitter -> Neu pro casse
* Erfolgt durch externe Tools -> kopieren aus Übungsdaten
* Definition durch Punkte, Flächen, Zellen, Boundaries  (mehrere geschlossene
    faces)

### ./constant/polyMesh/boundary

* **wichtig  für die Wahl der Randbedingungen**
```cpp
    EXTENSION_OUT
    {
        type        wall;
        nFaces      612;
        startFace   2968514;
    }
    EXTENSION_OUT
    {
        type        wall;
        nFaces      6624;
        startFace   2969126;
    }
```
* Steuerung der Randbedingungen durch Boundaries
* Verschiedene Basistypen

* **Base type** -> patch, wall
* **Primitive type** -> fixedValue, zeroGradient, ... 

## system-Files

### ./system/controlDict
* Grundlegende Solver-Funktionen
    * Solver selbst
    * Startzeitpunkt und zeit
    * Endzeitpunkt und zeit
    * Zeitschritt
    * Backupkriterium und Intervall

```cpp
application         icoFoam;

startFrom           startTime;

stopAt              endTime;

endTime             1;

deltaT              0.1;

writeControl        timeStep;

writeInterval       2;
```
* startFrom:
    * *firstTime* - erstes existierendes Zeitverzeichnis
    * *startTime* - durch startTime festgelegt
    * *latestTime*  - letztes existierendes Zeitverzeichnis

* stopAt:
    * *endTime* - durch endTime festgelegt 
    * *writeNow* - Backup schreiben und beenden
    * *noWriteNow* - kein Backup schreiben und beenden
    * *nextWrite* - bis zum nächsten definierten Zeitschritt

### ./system/fvSchemes
* Diskretisierungsverfahren zur Berechnung der partiellen Ableitungen festlegen
* Beispiel: Navier Stokes in OpenFOAM Syntax (fvm, fvc -> c++ klassen):
```cpp
fvm::ddt(rho, U) + fvm::div(phi, U)
            ==
-fvc::grad(p) + fvm::laplacian(mu, U)
```
Schemes:
```cpp
ddtSchemes
{
    //
}

gradSchemes
{
    //
}

divSchemes
{
    //
}

laplacianSchemes
{
    //
}
```
* ddtSchemes:
    * *CoEuler*
    * *CrankNicholson*
    * *Euler*
    * *SLTS*
    * *backward*
    * *bounded*
    * *localEuler*
    * *steadyState*

* gradSchemes:
    * *Gauss*
    * *cellLimited*
    * *cellMDLimited*
    * *edgeCellsLeastSquares*
    * *faceLimited*
    * *faceMDLimited*
    * *fourth*
    * *LeastSquares*
    * *pointCellLeastSquares*

* divSchemes + laplacianSchemes: nur *Gauss*
```cpp
interpolationSchemes
{
    //
}

snGradSchemes
{
    //
}

fluxRequired
{
    //
}
```
* interpolationSchemes:
    * MUSCL
    * QUICK
    * cubic
    * cubicUpwindFit
    * downwind
    * linear
    * upwind

* unbekanntes Schema -> OpenFoam gibt Liste aller verwendbaren Schemas aus

### ./system/fvSolution
* Festlegen der Lösungsverfahren
```cpp
solvers
{
    p
    {

    }

    U
    {

    }

    ...
}
```
* Jeder Variable benötigt einen Solver!
* symmetrische Matrizen: 
    * GAMG
    * ICCG
    * PCG
    * smoothSolver

* unsymmetrische Matrizen:
    * BICCG
    * GAMG
    * PBiCG
    * smoothSolver

* Druck Geschwindigkeitskopplung, z.B. Über SIMPLE  (Konvergenzkriterien möglich
    und Relaxationsfaktoren):
```cpp
PISO
{
    residualControl
    {

    }
}

relaxationFactoors
{

}
```
## Zeitverzeichnisse
* pro Variable (p,U etc.) jeweils eiigene Datei
* variables Mesh -> **./TimeStep/polyMesh/** -> bei großen Netzten ist
    Komprimiereung (**./system/controlDict/**) empfehlenswert
* **./0/** ist Initialverzeichnis

## Starten und Überwachen von Simulationen
-> Generell problemspezifische Solver -> bei Bedarf  können eigene Lösungen
implementiert werden

-> `pyfoamplotwatcher logfile` zum Überwachen von Simulationen

## $FOAM_UTILITIES
