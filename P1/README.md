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

I l'executem així: `run-main myDrawing.hs > ex0.svg`
Per visualitzar-lo introduïm la següent URL al nostre navegador: http://soft0.upc.edu/~ldatusrXX/practiques/prac1/ex0.svg

Modifiquem el programa canviant `myDrawing = solidCircle 1` per veure un cercle de color negre i radi 1.

Com que Haskell treballa amb programació funcional, si ara fessim una altre declaració de myDrawing: `myDrawing = solidCircle 2`, el compilador ens retornaria un error ja que en Haskell = no significa assignació, sinó definició, i per tant només podem definir una significat a cada variable.

Finalment editem el color del cercle, buscant a la documentació deñ mòdul Drawing trobem com es fa:
`myDrawing = colored green (solidCircle 1)` i tenim el següent output:
![green Solid Circle](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-03-16%20a%20les%2012.21.46.png)


**Exercici 2**
Realitzem un programa que dibuixi un semàfor:
```
import Drawing

topCircle c y = colored c (translated 0 y (solidCircle 1))
-- medCircle c y = colored c (translated 0 y (solidCircle 1))
botCircle c y = colored c (translated 0 y (solidCircle 1))


frame = rectangle 2.5 5
trafficLight = botCircle green (-1.1) <> topCircle red 1.1 <> frame

myDrawing :: Drawing
myDrawing = trafficLight

main = svgOf (myDrawing)
```

Si el compilem i mirem l'output que ens dona:
![green red Solid Circle](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-03-16%20a%20les%2012.46.06.png)


**Exercici 3**
Codi:
```
import Drawing
botCircle c = colored c (translated 0 (-1.5) (solidCircle 1))
botCircle c = colored c (translated 0 1.5 (solidCircle 1))
frame = rectangle 2.5 5

trafficLight = botCircle green <> topCircle red <> frame

lights 0 m=blank
lights n m = translated (3*n) m (trafficLight) <> lights (n-1) m

myDrawing = lights 3 0 <> lights 3 6 <> lights 3 12
main :: IO ()
main = svgOf myDrawing
```

Output:
![array semafors](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-03-16%20a%20les%2012.46.23.png)


**Exercici 4**
Codi:
```
import Drawing

trunk :: Drawing
trunk = polyline ([(0,0).(0,1)])

tree :: Int -> Drawing
tree 0 = colored yellow (circle 0.5)
tree m = trunk <> translated 0 1 (rotated(pi/10) branch) <> translated 0 1 (rotated(-pi/10) branch)
    where branch = tree (m-1)

myDrawing = tree 8
main :: IO ()
main = svgOf myDrawing
```

Output:
![arbre](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-03-16%20a%20les%2012.50.45.png)

**Exercici 5**

Fins ara hem estat fent repeticions d'un cert patró nosaltres mateixos, ara ho farem amb una funció `repeatDraw`, tot i que Haskell incorpora una funció que també resol aquest problema (`foldMap :: (Foldable t, Monoid m) => (a -> m) -> t a -> m`)

codi:
```
import Drawing
botCircle c = colored c (translated 0 (-1.5) (solidCircle 1))
topCircle c = colored c (translated 0 1.5 (solidCircle 1))
frame = rectangle 2.5 5

trafficLight = botCircle green <> topCircle red <> frame

repeatDraw :: (Int -> Drawing) -> Int -> Drawing
repeatDraw thing 0 = blank
repeatDraw thing n = thing n <> repeatDraw thing (n-1)

myDrawing = repeatDraw lightRow 3
lightRow :: Int -> Drawing
lightRow r = repeatDraw ( light r ) 3
light :: Int -> Int -> Drawing
light r c = translated ( 3*fromIntegral c - 6) ( 8*fromIntegral r-16) trafficLight

main :: IO ()
main = svgOf myDrawing
```

Output
![repeatDraw](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-03-16%20a%20les%2013.01.22.png)


**Exercici 6**

Donada una llista de n punts, dibuixar n semàfors situats en les corresponents posicions, usant les funcions *foldMap* i *trafficLight*.

codi
```
import Drawing
topCircle c = colored c (translated 0 (1.5) (solidCircle 1))
botCircle c = colored c (translated 0 (-1.5) (solidCircle 1))
frame = rectangle 2.5 5

trafficLight = botCircle green <> topCircle red <> frame

trafficLights :: [(Double, Double)] -> Drawing
light :: (Double, Double) -> Drawing
light r = translated (fst r) (snd r) trafficLight
trafficLights list = foldMap light list

cord = [(-9,9),(9,9),(0,9),(0,0),(-9,0),(9,0),(-9,-9),(9,-9),(0,-9)]

myDrawing :: Drawing
myDrawing = trafficLights cord

main :: IO ()
main = svgOf myDrawing
```
Output
![repeatDraw](https://github.com/akaKush/DAT_UPC/blob/main/P1/images/Captura%20de%20Pantalla%202021-03-16%20a%20les%2014.12.42.png)



### Segona Part: Interacció i modificació d'estat

