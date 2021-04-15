# Periodizität

## Translatorische Periodizität
* Case anlegen wie gewohnt, Pitz Daily Beispiel + geeignetes Netz

#### ./system/createPatchDict
-> Definiert die periodischen Randbedingungen. Zusammengeschlossene Flächen
müssen:
* Müssen vom Typ Patch sein
* kongruente Knoten aufweisen

-> Je Interfacepartner ein neuer Patch

```bash
cp $FOAM_UTILITIES/mesh/manipulation/createPatch/createPatchDict ./system
```
* angeben ob Knoten synchronisiert werden sollen
* Name und Partner des Interfaces
* Typ der  Periodizität
* Definition "separationVector"
* Toleranz der Knoten der Interface Partner

#### Abspeichern der Originaldateien
```bash
cp -r 0 0.orig
```

#### Netzgenerierung

* `createPatch` erzeugt die Periodizität und speicher das modifizierte Netz im
    ersten Zeitverzeichnis z.B. **./1/**
* `-overwrite` Flag überschreibt das ursrpüngliche Netz im
    **./constant/polyMesh/** Ordner
* Bei Mehrfacherstellung der Periodizität ist overwrite Flag nicht notwwendig

```bash
createPatch -overwrite
```
* Bei Initialisierung des neu erzeugten Patch muss Randbedingung zugewiesen
    werden

## Lokal verfeinertes Netz mit Startlösung
Initialisierung des case erfolgt über die Interpolation der Startlösung des
groben Netzes.
```bash
cp $FOAM_UTILITIES/preProcessing/mapFields/mapFieldsDict ./system
```
#### ./system/mapFieldsDict
Legt fest, welche boundaries des Bezugs-Case auf die entsprechenden boundaries
des ziel-case interpoliert werden sollen. 

Im Prinzip muss jedes Feld zu seinem korrespondierenden Feld gemappt werden:

```bash
patchMap
(
HUB HUB
SYM_XY SYM_XY
...
);

cuttingPatches
(

);
```
Mapping der Fields über (Vorher neues Mesh kopieren und Patches erstellen):
```bash
mapFields -sourceTime 100 ../040_TransPer
```
Bei identischer Benennung der Boundaries kann auf die mapFieldsDict verzichtet
werden und die Flag `-consistent` verwendet werden.

## Rotatorische Periodizität

#### ./system/createPatchDict
```bash
cp $FOAM_UTILITIES/mesh/manipulation/createPatch/createPatchDict ./system 
```
Ähnliche Konfiguration wie translatorische Periodizität -> Im Skript nachschauen
