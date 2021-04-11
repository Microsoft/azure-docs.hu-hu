---
title: 'Adatexportálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Designer adatexportálási moduljának használatával mentheti az eredményeket és a köztes adatAzure Machine Learningn kívüli adatmennyiséget.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 90755aef66fa51084d83d036722187b61449a6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656906"
---
# <a name="export-data-module"></a>Adatmodul exportálása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal mentheti az eredményeket, a köztes adatok és a munkafolyamatok adatait a Felhőbeli tárolási célhelyekre. 

Ez a modul támogatja az adatexportálást a következő felhőalapú adatszolgáltatásokban:

- Azure Blob-tároló
- Azure-fájlmegosztás
- 1. generációs Azure Data Lake Storage
- 2. generációs Azure Data Lake Storage
- Azure SQL-adatbázis

Az adatexportálás előtt először regisztrálnia kell egy adattárt a Azure Machine Learning munkaterületen. További információ: [hozzáférés az Azure Storage-szolgáltatásokban tárolt adatokhoz](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Az exportálási beállítások konfigurálása

1. Adja hozzá az **adatexportálási** modult a folyamathoz a tervezőben. Ez a modul a **bemeneti és a kimeneti** kategóriában található.

1. Az exportálni kívánt adategységeket tartalmazó modulba csatlakoztasson **adatexportálást** .

1. Válassza az **adatexportálás** lehetőséget a **Tulajdonságok** ablaktábla megnyitásához.

1. Az **adattár** mezőben válasszon ki egy meglévő adattárt a legördülő listából. Létrehozhat egy új adattárt is. Tekintse át, hogyan [érheti el az Azure Storage-szolgáltatásokban tárolt Access-adatbázisokat](../how-to-access-data.md).

    > [!NOTE]
    > Bizonyos adattípusok egy másik adattípusként megadott SQL Database-oszlopba való exportálása nem támogatott. A célként megadott táblának nem kell elsőként létezni.

1. A **kimenet újragenerált** jelölőnégyzete határozza meg, hogy végrehajtja-e a modult a kimenet újralétrehozásához a futási időben. 

    Alapértelmezés szerint nincs kiválasztva, ami azt jelenti, hogy ha a modult korábban ugyanazokkal a paraméterekkel hajtották végre, a rendszer a legutóbbi futtatásból a futási idő csökkentése érdekében újra fogja használni a kimenetet. 

    Ha be van jelölve, a rendszer ismét végrehajtja a modult a kimenet újralétrehozásához.

1. Adja meg az elérési utat abban az adattárban, ahol az adattároló található. Az elérési út relatív elérési út. Tegyük fel `data/testoutput` például, hogy az **exportálási adatok** bemeneti adatok a `data/testoutput` modul **kimeneti beállításaiban** beállított adattárba lesznek exportálva.

    > [!NOTE]
    > Az üres elérési utak vagy **URL-elérési utak** nem engedélyezettek.


1. **Fájlformátum** esetén válassza ki azt a formátumot, amelyben tárolni szeretné az adattárolást.
 
1. A folyamat elküldése.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
