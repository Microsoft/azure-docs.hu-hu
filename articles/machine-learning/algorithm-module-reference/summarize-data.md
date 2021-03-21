---
title: Adatok összegzése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki az adatkészletek oszlopaihoz tartozó alapszintű leíró statisztikai jelentést az Azure Machine Learning-ben az összegző adatok modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898211"
---
# <a name="summarize-data"></a>Adatok összegzése

Ez a cikk a Azure Machine Learning Designer modulját ismerteti.

Az összesítő adat modul használatával olyan szabványos statisztikai mértékeket hozhat létre, amelyek leírják a bemeneti tábla minden oszlopát.

Az összegző statisztika akkor hasznos, ha meg szeretné ismerni a teljes adatkészlet jellemzőit. Előfordulhat például, hogy tudnia kell a következőket:

- Hány hiányzó érték van az egyes oszlopokban?
- Hány egyedi érték van a funkció oszlopban?
- Mi az egyes oszlopok átlagos és szórása?

A modul kiszámítja az egyes oszlopok fontos pontszámait, és a bemenetként megadott összes változó (adatoszlop) összesítő statisztikájának sorát adja vissza.

## <a name="how-to-configure-summarize-data"></a>Az adatösszesítés konfigurálása  

1. Adja hozzá az **összesítő** adatmodult a folyamathoz. Ez a modul a Designer **statisztikai függvények** kategóriájában található meg.

1. Kapcsolja be azt az adatkészletet, amelyhez jelentést szeretne készíteni.

    Ha csak néhány oszlopra szeretne jelentést készíteni, használja az oszlopok [kiválasztása az adatkészlet](select-columns-in-dataset.md) modulban lehetőséget, hogy az oszlopok egy részhalmazát feldolgozza.

1. Nincs szükség további paraméterekre. Alapértelmezés szerint a modul a bemenetként megadott összes oszlopot elemzi, az oszlopok értékeinek típusától függően pedig az eredmények szakaszban leírtak szerint a megfelelő statisztikai adatokat [jeleníti](#results) meg.

1. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A modul jelentései a következő statisztikákat is tartalmazhatják. 

|Oszlop neve|Leírás|
|------|------|  
|**Szolgáltatás**|Az oszlop neve|
|**Száma**|Az összes sor száma|
|**Egyedi értékek száma**|Egyedi értékek száma az oszlopban|
|**Hiányzó értékek száma**|Egyedi értékek száma az oszlopban|
|**Min**|Legalacsonyabb érték az oszlopban|  
|**Max**|Legmagasabb érték az oszlopban|
|**Értem**|Az összes oszlop értékének középértéke|
|**Középérték szórása**|Oszlop értékeinek szórása|
|**1. kvartilis**|Érték az első kvartilis|
|**Medián**|Medián oszlop értéke|
|**harmadik kvartilis**|Érték a harmadik kvartilis|
|**Mód**|Oszlop értékeinek módja|
|**Tartomány**|A maximális és a minimális értékek közötti értékek számát jelző egész szám|
|**Minta eltérése**|Az oszlop eltérése; Lásd: Megjegyzés|
|**Minta szórása**|Oszlop szórása; Lásd: Megjegyzés|
|**Minta torzítása**|Az oszlop torzítása; Lásd: Megjegyzés|
|**Minta csúcsosságát**|Csúcsosságát az oszlophoz; Lásd: Megjegyzés|
|**P 0.5**|0,5% percentilis|
|**P1**|1% percentilis|
|**P5**|5%-os percentilis|
|**P95**|95% percentilis|
|**P 99,5**|99,5% percentilis |

## <a name="technical-notes"></a>Technikai megjegyzések

- A nem numerikus oszlopok esetében a rendszer csak a darabszám, az egyedi értékek száma és a hiányzó értékek értékét számítja ki. Más statisztikák esetében null értéket ad vissza.

- A logikai értékeket tartalmazó oszlopok a következő szabályok használatával lesznek feldolgozva:

    - A min érték kiszámításakor a logikai és a rendszer alkalmazza.
    
    - A Max számításakor logikai vagy alkalmazva
    
    - A számítási tartománynál a modul először ellenőrzi, hogy az oszlopban szereplő egyedi értékek száma 2-e.
    
    - A lebegőpontos számításokat igénylő statisztikai adatok feldolgozásakor az igaz értéket a 1,0-as értékként kezeli a rendszer, a hamis értékek pedig 0,0-ként lesznek kezelve.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .  
