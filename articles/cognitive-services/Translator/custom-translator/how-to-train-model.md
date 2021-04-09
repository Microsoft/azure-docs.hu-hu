---
title: Modell betanítása – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A modell betanítása fontos lépés a fordítási modellek létrehozásakor. Betanítás az adott képzésekhez kiválasztott dokumentumok alapján történik.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896426"
---
# <a name="train-a-model"></a>Modell betanítása

A modell betanítása az első és legfontosabb lépés a fordítási modell létrehozásához, ellenkező esetben a modell nem építhető ki. Betanítás a képzésekhez kiválasztott dokumentumok alapján történik. Ha kijelöli a "képzés" dokumentum típusát, a 10 000 párhuzamos mondatok minimális követelményének szem előtt tartása. A dokumentumok kiválasztása után a betanítási mondatok teljes számát jelenítjük meg. Ez a követelmény nem érvényes, ha csak a szótár típusú dokumentumok lehetőséget választja a modell betanításához.

Modell betanítása:

1. Válassza ki azt a projektet, amelyben modellt kíván létrehozni.

2. A projekthez tartozó adatlapon megjelenik a Project Language pár releváns dokumentuma. Manuálisan válassza ki a modell betanításához használni kívánt dokumentumokat. Kiválaszthatja a képernyőn megjelenő dokumentumok tanítását, finomhangolását és tesztelését. Azt is megteheti, hogy kiválasztja a betanítási készletet, és egyéni fordítóval létrehozta a hangolási és tesztelési készleteket.

    - Dokumentum neve: a dokumentum neve.

    - Párosítás: Ha ez a dokumentum egy párhuzamos vagy egynyelvű dokumentum. Az egynyelvű dokumentumok jelenleg nem támogatottak a betanításhoz.

    - Dokumentum típusa: képzés, hangolás, tesztelés vagy szótár lehet.

    - Nyelvi pár: a projekt forrás-és célként megadott nyelvét jeleníti meg.

    - Forrás mondatok: a forrásfájlban kinyert mondatok számát jeleníti meg.

    - Cél mondatok: a célfájlből kinyert mondatok számát jeleníti meg.

    ![Modell betanítása](media/how-to/how-to-train-model.png)

3. Kattintson a "modell létrehozása" gombra.

4. A párbeszédpanelen adja meg a modell nevét. Alapértelmezés szerint a "betanítás azonnal" lehetőség van kiválasztva a betanítási folyamat elindításához, amikor a "modell létrehozása" gombra kattint. A "Mentés piszkozatként" lehetőség kiválasztásával létrehozhatja a modell metaadatait, és a modellt vázlat állapotban helyezheti el, de a modell tanítása nem indul el. Egy későbbi időpontban manuálisan kell kiválasztani a modelleket a vázlat állapotból a betanításhoz.

5. Kattintson a "modell létrehozása" gombra.

    ![A tanítási modell párbeszédablaka](media/how-to/how-to-train-model-2.png)

6. Az egyéni fordító beküldi a képzést, és megjeleníti a képzés állapotát a modellek lapon.

    ![Modell betanítása lap](media/how-to/how-to-train-model-3.png)

>[!Note]
>Az egyéni fordító egyszerre 10 egyidejű betanítást támogat egy adott munkaterületen belül.

## <a name="modify-a-model-name"></a>Modell nevének módosítása

A modell neve a modell részletei lapon módosítható.

1. A projektek lapon kattintson a projekt nevére, ahol a modell létezik.
2. Kattintson a modell fülre.
3. Kattintson a modell nevére a modell részleteinek megtekintéséhez.
4. Kattintson a ceruza ikonra.

    ![Modell szerkesztése](media/how-to/how-to-edit-model.png)

5. A párbeszédpanelen módosítsa a modell nevét, és adjon meg egy értelmes nevet a modellnek.

    ![További párbeszédpanel szerkesztése](media/how-to/how-to-edit-model-dialog.png)

6. Kattintson a Mentés gombra.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan tekintheti meg a modell részleteit](how-to-view-model-details.md).
