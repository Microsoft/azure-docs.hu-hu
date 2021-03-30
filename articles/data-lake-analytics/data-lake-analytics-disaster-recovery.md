---
title: Vész-helyreállítási útmutató a Azure Data Lake Analytics
description: Megtudhatja, hogyan tervezheti meg a Azure Data Lake Analytics-fiókok vész-helyreállítását.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220975"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Vész-helyreállítási útmutató a Azure Data Lake Analytics

Az Azure Data Lake Analytics egy igény szerinti elemzési feladatokat végző szolgáltatás, amely leegyszerűsíti a big data-elemzéseket. Az üzembe helyezés, a konfigurálás és a hardver beállítása helyett lekérdezéseket írhat az adatátalakítás és az értékes információk kinyerése érdekében. Az elemző szolgáltatás bármekkora munkát képes elvégezni, csak be kell állítania, hogy mekkora teljesítményre van szüksége. Költséghatékony megoldás, hiszen csak az elvégzett munkáért kell fizetni. Ez a cikk útmutatást nyújt arról, hogyan lehet felvenni a feladatokat a ritka régiókban vagy véletlen törlésekben.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató

Azure Data Lake Analytics használatakor fontos, hogy előkészítse a saját vész-helyreállítási tervét. Ez a cikk segítséget nyújt a vész-helyreállítási terv létrehozásához. További források is létrehozhatók a saját csomag létrehozásához:
- [Azure-alkalmazások hibaelhárítása és vészhelyreállítása](/azure/architecture/reliability/disaster-recovery)
- [Műszaki útmutató az Azure rugalmasságáról](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Ajánlott eljárások és forgatókönyvek – útmutató

Egy ismétlődő U-SQL-feladatot futtathat egy ADLA-fiókban egy olyan régióban, amely U-SQL-táblákat, valamint strukturálatlan adatokat olvas és ír.  Készítse elő a katasztrófát a következő lépések elvégzésével:

1. Hozzon létre ADLA-és ADLS-fiókokat a másodlagos régióban, amelyet egy leállás során fog használni.

   > [!NOTE]
   > Mivel a fiókok nevei globálisan egyediek, egységes elnevezési sémát használunk, amely azt jelzi, hogy melyik fiók másodlagos.

2. Strukturálatlan adat esetén a vész [-helyreállítási útmutatás a Azure Data Lake Storage Gen1ban található adathoz](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. A ADLA-táblákban és-adatbázisokban tárolt strukturált adatok esetében hozzon létre másolatokat a metaadatokból, például adatbázisokból, táblákból, táblázat értékű függvényekből és szerelvényekről. Rendszeres időközönként újra kell szinkronizálnia ezeket az összetevőket, amikor a változások az éles környezetben történnek. Például az újonnan beszúrt adatoknak a másodlagos régióba való replikálásához az adatok másolásával és a másodlagos táblába való beillesztéssel kell replikálni.

   > [!NOTE]
   > Ezek az objektumok nevei a másodlagos fiókra vannak korlátozva, és nem globálisan egyediek, így ugyanazokat a neveket használhatják, mint az elsődleges éles fiókban.

Leállás közben frissítenie kell a parancsfájlokat, hogy a bemeneti elérési utak a másodlagos végpontra mutassanak. Ezután a felhasználók elküldik a feladatait a ADLA-fióknak a másodlagos régióban. A rendszer ezután a ADLA és a ADLS-fiókba írja a feladatot a másodlagos régióban.

## <a name="next-steps"></a>Következő lépések

[Vész-helyreállítási útmutató a Azure Data Lake Storage Gen1 lévő adatkezeléshez](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
