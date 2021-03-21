---
title: 'Szöveg előfeldolgozása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható a szöveg megtisztítása és leegyszerűsítése Azure Machine Learning Designer előfeldolgozási moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659283"
---
# <a name="preprocess-text"></a>Szöveg előfeldolgozása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

A szöveg megtisztításához és leegyszerűsítéséhez használja az **előfeldolgozási szöveg** modult. Támogatja ezeket a gyakori szöveg-feldolgozási műveleteket:

* Leállítás – szavak eltávolítása
* Adott cél karakterláncok keresése és cseréje reguláris kifejezések használatával
* Morfológiai elemzéshez, amely több kapcsolódó szót konvertál egyetlen kanonikus űrlapra
* Kis-és nagybetűk normalizálása
* Bizonyos osztályok, például számok, speciális karakterek és ismétlődő karakterek (például "AAAA") eltávolítása
* E-mailek és URL-címek azonosítása és eltávolítása

Az **előfeldolgozó szöveges** modul jelenleg csak az angol nyelvet támogatja.

## <a name="configure-text-preprocessing"></a>Szöveg előfeldolgozásának konfigurálása  

1.  Adja hozzá az **előfeldolgozó szöveg** modult a folyamathoz Azure Machine learning. Ez a modul a **text Analytics** alatt található.

1. Csatlakoztasson egy olyan adatkészletet, amelynek legalább egy oszlopa tartalmaz szöveget.

1. Válassza ki a nyelvet a **nyelv** legördülő listából.

1. **Tiszta szöveg oszlop**: válassza ki az elődolgozni kívánt oszlopot.

1. **Leállítási szavak eltávolítása**: akkor válassza ezt a lehetőséget, ha előre definiált nem indexelendő szót-listát kíván alkalmazni a Text (szöveg) oszlopra. 

    A nem indexelendő szót listája nyelvtől függő és testreszabható.

1. **Morfológiai elemzéshez**: akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a szavak a kanonikus űrlapon megjelenjenek. Ez a beállítás akkor hasznos, ha csökkenteni szeretné az egyéb hasonló szöveges tokenek egyedi előfordulásának számát.

    A morfológiai elemzéshez folyamat nagymértékben nyelvtől függ.

1. **Mondatok észlelése**: akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a modul az elemzés végrehajtásakor beillesszen egy mondathoz tartozó határt.

    Ez a modul három cső karakterből álló sorozatot használ `|||` a lezáró mondat megjelenítéséhez.

1. Az opcionális keresési és lecserélési műveleteket reguláris kifejezésekkel hajthatja végre. A reguláris kifejezés először a többi beépített beállítás előtt lesz feldolgozva.

    * **Egyéni reguláris kifejezés**: határozza meg a keresett szöveget.
    * **Egyéni helyettesítő sztring**: egyetlen helyettesítő érték definiálása.

1. Kis **-és nagybetűk normalizálása**: válassza ezt a beállítást, ha ASCII nagybetűs karaktereket szeretne a kisbetűs űrlapjaira konvertálni.

    Ha a karakterek nincsenek normalizálva, a kis-és nagybetűket tartalmazó szó két különböző szónak tekintendő.

1. A következő típusokat is eltávolíthatja a feldolgozott kimeneti szövegből:

    * **Számok eltávolítása**: válassza ezt a lehetőséget, ha el szeretné távolítani a megadott nyelv összes numerikus karakterét. Az azonosító számok tartományra és nyelvtől függenek. Ha a numerikus karakterek egy ismert szó szerves részét képezik, előfordulhat, hogy a számot nem távolítja el a rendszer. További információ a [technikai megjegyzésekben](#technical-notes).
    
    * **Speciális karakterek eltávolítása**: ezzel a beállítással távolíthatja el a nem alfanumerikus speciális karaktereket.
    
    * **Duplikált karakterek eltávolítása**: Ha ezt a beállítást választja, akkor a több mint kétszeresére ismétlődő sorokban távolítsa el a felesleges karaktereket. Például az "AAAAA" karakterláncot az "AA" értékre kell csökkenteni.
    
    * **E-mail-címek eltávolítása**: ezzel a beállítással távolíthatja el a formátum tetszőleges sorszámát `<string>@<string>` .  
    * **URL-címek eltávolítása**: akkor válassza ezt a lehetőséget, ha el szeretné távolítani az alábbi URL-előtagokat tartalmazó sorozatot: `http` , `https` , `ftp` , `www`
    
1. Művelet-összevonások **kibontása**: Ez a beállítás csak olyan nyelvekre vonatkozik, amelyek művelet-összevonásokat használnak; jelenleg csak angol nyelven érhető el. 

    A beállítás kiválasztásával például lecserélheti a *"nem maradok"* kifejezést a *"ne* maradjon ott" kifejezésre.

1. **Fordított perjelek normalizálása a perjelhez**: válassza ezt a lehetőséget, ha a összes példányát le `\\` szeretné képezni `/` .

1. **Tokenek felosztása speciális karaktereknél**: akkor válassza ezt a lehetőséget, ha a szavakat olyan karaktereken szeretné megszüntetni, mint például `&` , stb `-` . Ez a beállítás a speciális karaktereket is csökkentheti, ha kétszer ismétlődik. 

    A karakterlánc például három tokenre, a, és elemre `MS---WORD` lesz elválasztva `MS` `-` `WORD` .

1. A folyamat elküldése.

## <a name="technical-notes"></a>Technikai megjegyzések

A Studio (klasszikus) és a Designer **előfeldolgozó-szöveges** modulja különböző nyelvi modelleket használ. A tervező egy többfeladatos CNN betanított modellt használ a [térbeli](https://spacy.io/models/en)adatokból. A különböző modellek különböző tokenizer és beszédfelismerési fogókat biztosítanak, amelyek különböző eredményekhez vezetnek.

Az alábbiakban néhány példát láthat:

| Konfiguráció | Kimenet eredménye |
| --- | --- |
|Minden kiválasztott lehetőséggel </br> Magyarázat: </br> A "3test", például a "WC-3 3test 4test" esetében a tervező eltávolítja a teljes "3test" szót, mivel ebben a kontextusban a "3test" a tokent adja meg számként, és a rész-a beszédnek megfelelően eltávolítja a modult.| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="Minden kiválasztott lehetőséggel" border="True"::: |
|Csak a `Removing number` kijelölt </br> Magyarázat: </br> Az olyan esetekben, mint a "3test", a "4-EC", a Designer tokenizer dózisa nem osztja szét ezeket az eseteket, és a teljes tokenként kezeli őket. Így nem távolítja el ezeket a szavakat.| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="Csak &quot;eltávolítási szám&quot; van kiválasztva" border="True"::: |

A reguláris kifejezés használatával a testreszabott eredmények is kiadhatók:

| Konfiguráció | Kimenet eredménye |
| --- | --- |
|Minden kiválasztott lehetőséggel </br> Egyéni reguláris kifejezés: `(\s+)*(-|\d+)(\s+)*` </br> Egyéni helyettesítő sztring: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="Minden kiválasztott beállítás és reguláris kifejezés" border="True"::: |
|Csak a `Removing number` kijelölt </br> Egyéni reguláris kifejezés: `(\s+)*(-|\d+)(\s+)*` </br> Egyéni helyettesítő sztring: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="Kijelölt számok és reguláris kifejezés eltávolítása" border="True"::: |


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 