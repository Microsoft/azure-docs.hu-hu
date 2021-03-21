---
title: Modell részleteinek megtekintése – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A modellek lap bármely projektben megjeleníti az egyes modellek részleteit, például a modell nevét, a modell állapotát, a BLEU pontszámát, a képzést, a hangolást, a tesztelési mondatok számát.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 11f39aa480e3ba6508bf730c61891e80ea689e8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895951"
---
# <a name="view-model-details"></a>Modell részleteinek megtekintése

A Project alatti modellek lap a projekt összes modelljét megjeleníti. Ezen a lapon a projekthez betanított összes modell szerepel.

A projekt egyes modelljeinél ezek a részletek jelennek meg.

1. Modell neve: egy adott modell modelljének nevét jeleníti meg.

2. Állapot: egy adott modell állapotát jeleníti meg. Az új képzés a beküldött állapotba kerül, amíg el nem fogadják. Az állapot az adatfeldolgozásra változik, miközben a szolgáltatás kiértékeli a dokumentumok tartalmát. Ha a dokumentumok kiértékelése befejeződött, az állapot fut értékre változik, és megtekintheti a képzés részét képező mondatok számát, beleértve az automatikusan létrehozott hangolási és tesztelési csoportokat is. Alább látható a modellek állapotát leíró modell állapotának listája.

    - Elküldve: Megadja, hogy a háttérrendszer dolgozza fel a modell dokumentumait.

    - TrainingQueued: azt adja meg, hogy a betanítás várólistára kerüljön-e az MT rendszer számára a modell számára.

    - Futtatás: azt adja meg, hogy a képzés az MT rendszeren fut a modellhez.

    - Sikeres: azt adja meg, hogy az MT rendszer sikeres betanítása és a modell elérhető. Ebben az állapotban az adott modellhez tartozó BLEU-pontszám jelenik meg.

    - Üzembe helyezett: azt adja meg, hogy a sikeres betanított modellt az MT rendszer számára telepítse.

    - Központi telepítés: azt adja meg, hogy a telepített modell telepítése megtörténjen.

    - Nem telepített: azt adja meg, hogy a modell központi telepítési folyamata sikeresen befejeződött.

    - A képzés sikertelen: a betanítás sikertelen volt. Ha betanítási hiba történik, próbálja megismételni a betanítási feladatot. Ha a hiba továbbra is fennáll, vegye fel velünk a kapcsolatot. Ne törölje a sikertelen modellt.

    - DataProcessingFailed: azt adja meg, hogy az adatfeldolgozás nem sikerült egy vagy több olyan dokumentumhoz, amely a modellhez tartozik.

    - DeploymentFailed: azt adja meg, hogy a modell központi telepítése sikertelen volt.

    - MigratedDraft: azt adja meg, hogy a modell vázlat állapotban van-e a hub-ról az egyéni fordítóra való Migrálás után.

3. BLEU pontszáma: megmutatja a modell (a kétnyelvű próbaverzió) pontszámát, amely a fordítási rendszerek minőségét mutatja. Ez a pontszám azt mutatja be, hogy a fordítási rendszer milyen módon hajtja végre a betanítás eredményeként létrejövő, a tesztelési adatkészletben szereplő hivatkozási mondatokat. A BLEU pontszám akkor jelenik meg, ha a képzés sikeresen befejeződött. Ha a képzés nem fejeződött be/nem sikerült, a BLEU egyik pontszáma sem jelenik meg.

4. Betanítási mondatok száma: a betanítási készletként használt mondatok teljes számát jeleníti meg.

5. Hangolási mondatok száma: a hangolási készletként használt mondatok teljes számát jeleníti meg.

6.  Betanítási mondatok száma: a tesztelési csoportként használt mondatok teljes számát jeleníti meg.

7.  Mono-mondatok száma: a Mono-készletként használt mondatok teljes számát jeleníti meg.

8.  Üzembe helyezési művelet gomb: a sikeresen betanított modell esetében az "üzembe helyezés" gomb jelenik meg, ha nincs telepítve. Ha egy modellt telepítenek, megjelenik a "telepítés megkerülés" gomb.

9. Törlés: ezt a gombot használhatja, ha törölni szeretné a modellt. A modell törlése nem törli a modell létrehozásához használt dokumentumokat.

    ![Modell részleteinek megtekintése](media/how-to/how-to-view-model-details.png)

>[!Note]
>Ugyanazon rendszerek egymást követő betanításának összehasonlításához fontos, hogy megőrizze a hangolási készletet és a tesztelési készlet állandó értékét.

## <a name="view-model-training-details"></a>Modell tanítási adatainak megtekintése

A képzés befejezésekor a részletek oldalon áttekintheti a képzés részleteit. Válasszon ki egy projektet, keresse meg és válassza ki a modellek fület, és válassza ki a modellt.

A modell lapon két lap található: képzés részletei és tesztelés.

1.  **Képzés részletei:** Ezen a lapon a képzésben használt dokumentum (ok) listája látható:

    -  Dokumentumok neve: ebben a mezőben a dokumentum neve látható

    -  Dokumentum típusa: ebben a mezőben látható, hogy a dokumentum párhuzamos/monó-e.

    -  Mondatok száma a forrás nyelvén: ebben a mezőben a mondatok száma látható a forrás nyelvének részeként.

    -  Mondatok száma a célnyelv mezőben: ebben a mezőben a mondatok száma látható a célnyelv részeként.

    -  Igazított mondatok: ebben a mezőben láthatók a mondatok számának igazítása az egyéni fordítóban az igazítási folyamat során.

    -  Használatban lévő mondatok: ebben a mezőben a mondatok számát jeleníti meg az egyéni fordító a képzés során.

    ![Modell képzésének részletei](media/how-to/how-to-model-training-details.png)

2.  **Teszt:** Ezen a lapon látható a sikeres betanítási teszt részletei.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [teszteredmények](how-to-view-system-test-results.md) eredményeit, és elemezze a képzési eredményeket.
