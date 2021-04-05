# Pràctica 1 DAT

En aquesta primera pràctica de DAT, veurem una introducció a **Haskell** i la **programació funcional**.

Per poder-la iniciar ens connectem per ssh al laboratori de DAT:

`ssh -p 6767 ldatusrXX@soft00.upc.edu`

Un cop dins necessitem crear i editar l'arxiu `.bash_profile` amb l'editor de textos q vulgueu, jo he fet servir nano, i afegim la següent línia: `PATH="$PATH:~WEBprofe/usr/bin"`

Llavors executem `source .bash_profile` i ja podem iniciar les pràctiques.

## Pràctica

Creem el directori de les pràctiques `cd practiques` i el de la `prac1` dins d'aquest.

Per executar les pràctiques utilitzarem l'scrip `WEBprofe/usr/bin/run-main` que compila i executa un programa Haskell tq:
`$ run-main fitxer_codi.hs > sortida.svg`

La sortida del programa es guarda en el fitxer `sortida.svg` i la podreu visualitzar amb qualsevol navegador que suporti SVG.

### Primera part

Creem un fitxer haskell anomenat myDrawing.hs amb el següent codi:
```
import Drawing

myDrawing :: Drawing
myDrawing = blank

main :: IO ( )
main = svgOf myDrawing
```

I el compilem i executem així: `run-main myDrawing.hs > ~/publich_html_/prac1/ex0.svg`
Per visualitzar-lo introduïm la següent URL al nostre navegador: http://soft0.upc.edu/~ldatusrXX/practiques/prac1/ex0.svg amb el nostre usuari de dat en comptes de les XX.

Modifiquem el programa canviant `myDrawing = solidCircle 1` per veure un cercle de color negre i radi 1.

Com que Haskell treballa amb programació funcional, si ara fessim una altre declaració de myDrawing: `myDrawing = solidCircle 2`, el compilador ens retornaria un error ja que en Haskell = no significa assignació, sinó definició, i per tant només podem definir una significat a cada variable.

Finalment editem el color del cercle, buscant a la documentació deñ mòdul Drawing trobem com es fa:
`myDrawing = colored green (solidCircle 1)` i tenim el següent output:
![green Solid Circle](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-03-16%20a%20les%2012.21.46.png)


**Exercici 2**
Realitzem un programa que dibuixi un semàfor:
```

import Drawing

colorCircle c y = colored c (translated 0 y (solidCircle 1))

frame = rectangle 2.5 7.5

trafficLight = frame <> colorCircle green (-2.5) <> colorCircle yellow 0 <> colorCircle red 2.5

main :: IO ()
main = svgOf myDrawing


```

Si el compilem i mirem l'output que ens dona:
![green red Solid Circle](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-04-05%20a%20les%2013.50.00.png)


**Exercici 3**

He anat modificant el codi del fitxer `myDrawing.hs` en cada exercici, per això és possible que només veiem un fitxer per a tots els exercicis de la primera part.

Codi:
```
import Drawing

colorCircle c y = colored c (translated 0 y (solidCircle 1))
frame = rectangle 2.5 7.5
trafficLight = frame <> colorCircle green (-2.5) <> colorCircle yellow 0 <> colorCircle red 2.5

lights :: Int -> Drawing
lights 0 = blank
lights n = translated (3 * fromIntegral n) 0 trafficLight <> lights (n-1)

myDrawing = lights 3

main :: IO ()
main = svgOf myDrawing

```

Output:
![array semafors](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-04-05%20a%20les%2013.55.40.png)

Ara que tenim una fila de 3, anem a fer la columna també de 3:
```
import Drawing

colorCircle c y = colored c (translated 0 y (solidCircle 1))
frame = rectangle 2.5 7.5
trafficLight = frame <> colorCircle green (-2.5) <> colorCircle yellow 0 <> colorCircle red 2.5

lights :: Int -> Drawing
lights (-2) = blank
lights n = translated (3 * fromIntegral n) 0 trafficLight <> lights (n-1)
row y = (translated 0 (y) (lights 1))
matrix = row 8 <> row 0 <> row (-8)

myDrawing = matrix

main :: IO ()
main = svgOf myDrawing
```

Output:
![array semafors](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-04-05%20a%20les%2015.56.44.png)


**Exercici 4**

Per aquest exercici si que he creat un altre fitxer ja que és diferent als dels semàfors.

Codi:
```
import Drawing

trunk :: Drawing
trunk = polyline ([(0,0),(0,1)])

tree :: Int -> Drawing
tree 0 = colored yellow (circle 0.25)
tree m = trunk <> translated 0 1 (rotated(pi/10) branch) <> translated 0 1 (rotated(-pi/10) branch)
    where branch = tree (m-1)

myDrawing = tree 8
main :: IO ()
main = svgOf myDrawing
```

Afegeixo directament la foto amb les flors, sense només caldria posar m = 0.

Output:
![arbre](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-04-05%20a%20les%2015.49.35.png)

**Exercici 5**

Fins ara hem estat fent repeticions d'un cert patró nosaltres mateixos, ara ho farem amb una funció `repeatDraw`, tot i que Haskell incorpora una funció que també resol aquest problema (`foldMap :: (Foldable t, Monoid m) => (a -> m) -> t a -> m`)

codi:
```
import Drawing

colorCircle c y = colored c (translated 0 y (solidCircle 1))
frame = rectangle 2.5 7.5
trafficLight = frame <> colorCircle green (-2.5) <> colorCircle yellow 0 <> colorCircle red 2.5

repeatDraw :: (Int -> Drawing) -> Int -> Drawing
repeatDraw thing 0 = blank
repeatDraw thing n = thing n <> repeatDraw thing (n-1)

myDrawing = repeatDraw lightRow 3
lightRow :: Int -> Drawing
lightRow r = repeatDraw (light r) 3
light :: Int -> Int -> Drawing
light r c = translated (3 * fromIntegral c - 6) (8*fromIntegral r-16) trafficLight


main :: IO ()
main = svgOf myDrawing
```
Executem `run-main myDrawing.hs > ~/public_html_/prac1/ex5.svg`

Output
![repeatDraw](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-04-05%20a%20les%2015.56.44.png)


**Exercici 6**

Donada una llista de n punts, dibuixar n semàfors situats en les corresponents posicions, usant les funcions *foldMap* i *trafficLight*.

codi
```
import Drawing

colorCircle c y = colored c (translated 0 y (solidCircle 1))
frame = rectangle 2.5 7.5
trafficLight = frame <> colorCircle green (-2.5) <> colorCircle yellow 0 <> colorCircle red 2.5

trafficLights :: [(Double, Double)] -> Drawing
light :: (Double, Double) -> Drawing
light r = translated (fst r) (snd r) trafficLight
trafficLights list = foldMap light list

cord = [(0,0), (0,9), (0,-9), (9, 0), (-9, 0), (9,9), (-9,9), (9, -9), (-9, -9)]

myDrawing :: Drawing
myDrawing = trafficLights cord 

main :: IO ()
main = svgOf myDrawing
```
Output
![repeatDraw](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-04-05%20a%20les%2016.05.18.png)



### Segona Part: Interacció i modificació d'estat

