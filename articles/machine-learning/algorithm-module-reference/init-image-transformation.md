---
title: Init-rendszerkép – Transformationply képátalakítás
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy miként inicializálható a képátalakítás a Azure Machine Learning Designer init rendszerkép-átalakítási moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421277"
---
# <a name="init-image-transformation"></a>Képátalakítás indítása

Ez a cikk azt ismerteti, hogyan használható az **init rendszerkép-átalakítási** modul a Azure Machine learning Designerben, hogy inicializálja a képátalakítást annak meghatározásához, hogyan szeretné átalakítani a képet.

## <a name="how-to-configure-init-image-transformation"></a>Az init-rendszerkép átalakításának konfigurálása

1.  Adja hozzá az **init rendszerkép-átalakítási** modult a folyamathoz a tervezőben. 

2.  Az **átméretezéshez** adja meg, hogy át kívánja-e méretezni a bemeneti PIL-képet a megadott méretre. Ha az "igaz" lehetőséget választja, akkor a kívánt kimeneti képméretet a 256 **alapértelmezett méretben adhatja** meg. 

3.  A **Center Crop** esetében adja meg, hogy az adott PIL-képet a középpontban szeretné-e kivágni. Ha az "igaz" lehetőséget választja, megadhatja a termés **méretének** kívánt kimeneti képméretet, alapértelmezés szerint 224.  

4.  A **pad** beállításnál adja meg, hogy a megadott PIL-képet minden oldalon a 0 értékű pad értékkel kívánja-e ellátni. Ha a "true" (igaz) lehetőséget választja, megadhatja a kitöltés (a hozzá tartozó képpontok számát) a **kitöltés** minden szegélyén.

5.  A **szín vibrálása beállításnál** megadhatja, hogy a rendszer véletlenszerűen módosítja-e a kép fényességét, kontrasztját és telítettségét.

6.  A **szürkeárnyalatos** beállításnál megadhatja, hogy a képet szürkeárnyalatos szeretné-e konvertálni.

7.  A **véletlenszerű átméretezett termés** esetében adja meg, hogy a megadott PIL-kép véletlenszerű méretre és méretarányra van-e leképezve. A véletlenszerű méret (0,08 és 1,0 közötti tartomány) és az eredeti oldalarány (3/4 – 4/3 közötti tartomány) termése. Ez a termés végül átméretezi a megadott méretet.
    Ezt gyakran használják a kezdeti hálózatok betanításához. Ha az "igaz" értéket választja, akkor az egyes élek várt kimeneti mérete **véletlenszerűen**, alapértelmezés szerint 256.

8.  **Véletlenszerű körülvágás** esetén adja meg, hogy a megadott PIL-képet véletlenszerűen szeretné-e kivágni. Ha az "igaz" értéket választja, a termés kívánt kimeneti mérete **véletlenszerűen**, alapértelmezés szerint 224.

9.  **Véletlenszerű vízszintes tükrözés** esetén adja meg, hogy a megadott PIL-képet vízszintesen kell-e véletlenszerűen megfordítani a 0,5-as valószínűséggel.

10.  **Véletlenszerű Függőleges tükrözés** esetén adja meg, hogy a megadott PIL-képet függőlegesen kell-e véletlenszerűen kifordítani a 0,5-as valószínűséggel.

11.  **Véletlenszerű rotáció** esetén határozza meg, hogy a kép szög szerint legyen elforgatva. Ha "true" (igaz) értéket választ, a különböző mértékeket a **véletlenszerű rotációs fok** beállításával adhatja meg, ami a következőt jelenti: (fok, + fok), alapértelmezés szerint 0.

12.  **Véletlenszerű affin** esetén határozza meg, hogy véletlenszerű affin-e a Képközpont invariánsának átalakítása. Ha "true" (igaz) értéket választ, megadhatja a különböző mértékeket a **véletlenszerű affin fok** alapján, ami a következőt jelenti: (fok, + fok), alapértelmezés szerint 0.

13.  A **véletlenszerű szürkeárnyalatos** beállításnál megadhatja, hogy a rendszer véletlenszerűen konvertálja-e a képet szürkeárnyalatos értékre a 0,1

14.  **Véletlenszerű perspektíva** esetén adja meg, hogy a megadott PIL-rendszerkép perspektivikus átalakítása a 0,5-es valószínűséggel véletlenszerűen történjen-e.


16.  Kapcsolódás a [rendszerkép-átalakítási modul alkalmazásához](apply-image-transformation.md) a fent megadott átalakítás a bemeneti adatkészletre való alkalmazásához.

17. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

Az átalakítás befejezése után a [rendszerkép-átalakítási](apply-image-transformation.md) modul kimenetében megtalálhatja az átalakított lemezképeket.


## <a name="technical-notes"></a>Technikai megjegyzések  

[https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html)További információ a képek átalakításáról:.

###  <a name="module-parameters"></a>Modul paramétereinek  

| Name                    | Tartomány   | Típus    | Alapértelmezett | Description                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| Átméretezés                  | Bármelyik     | Logikai | Igaz    | A bemeneti PIL-rendszerkép átméretezése a megadott méretre |
| Méret                    | >= 1     | Egész szám | 256     | A kívánt kimeneti méret megadása          |
| Középpont – növénytermesztés             | Bármelyik     | Logikai | Igaz    | Megvágja a megadott PIL-képet a központban  |
| Vágási méret               | >= 1     | Egész szám | 224     | A körülvágás kívánt kimeneti méretének megadása |
| Pad                     | Bármelyik     | Logikai | Hamis   | A megadott PIL-képet a megadott "pad" értékkel rendelkező összes oldalon pad |
| Kitöltés                 | >= 0     | Egész szám | 0       | Kitöltés az egyes szegélyeken                   |
| Szín vibrálása            | Bármelyik     | Logikai | Hamis   | A kép fényességének, kontrasztjának és telítettségének véletlenszerű módosítása |
| Szürkeárnyalatos               | Bármelyik     | Logikai | Hamis   | Rendszerkép átalakítása szürkeárnyalatos               |
| Véletlenszerűen átméretezett termés     | Bármelyik     | Logikai | Hamis   | A megadott PIL-kép véletlenszerű méretének és oldalarányának kivágása |
| Véletlenszerű méret             | >= 1     | Egész szám | 256     | Az egyes szegélyek várható kimeneti mérete        |
| Véletlenszerű körülvágás             | Bármelyik     | Logikai | Hamis   | A megadott PIL-kép kivágása véletlenszerű helyen |
| Véletlenszerű körülvágási méret        | >= 1     | Egész szám | 224     | A termés kívánt kimeneti mérete          |
| Véletlenszerű vízszintes tükrözés  | Bármelyik     | Logikai | Igaz    | A megadott PIL-rendszerkép vízszintes tükrözése egy adott valószínűséggel |
| Véletlenszerű Függőleges tükrözés    | Bármelyik     | Logikai | Hamis   | A megadott PIL-képek függőleges tükrözése véletlenszerűen egy adott valószínűséggel |
| Véletlenszerű rotáció         | Bármelyik     | Logikai | Hamis   | A kép elforgatása szög alapján                |
| Véletlenszerű rotációs fok | [0180] | Egész szám | 0       | A kiválasztani kívánt fok tartománya          |
| Véletlenszerű affin           | Bármelyik     | Logikai | Hamis   | Véletlenszerű affin-átalakítás a Képközpont invariánsával |
| Véletlenszerű affin fok   | [0180] | Egész szám | 0       | A kiválasztani kívánt fok tartománya          |
| Véletlenszerű szürkeárnyalatos        | Bármelyik     | Logikai | Hamis   | A rendszerképet véletlenszerűen konvertálhatja szürkeárnyalatos a 0,1-as valószínűséggel |
| Véletlenszerű perspektíva      | Bármelyik     | Logikai | Hamis   | A megadott PIL-rendszerkép perspektivikus átalakítását hajtja végre véletlenszerűen a 0,5-as valószínűséggel |
| Véletlenszerű törlés          | Bármelyik     | Logikai | Hamis   | Véletlenszerűen kiválaszt egy téglalap-régiót egy képen, és törli a képpontot a 0,5 valószínűséggel |

###  <a name="output"></a>Kimenet  

| Név                        | Típus                    | Description                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| Kimeneti rendszerkép átalakítása | TransformationDirectory | A rendszerkép- **átalakítási modul alkalmazásához** csatlakoztatható kimeneti képátalakítás. |

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 