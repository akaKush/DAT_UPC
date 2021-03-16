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
![green Solid Circle](Dragster.jpg)

