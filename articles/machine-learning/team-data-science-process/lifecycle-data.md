---
title: A csoportos adatelemzési folyamat adatgyűjtése és megértése
description: Az adatgyűjtéssel és az adatelemzési projektekkel kapcsolatos célok, feladatok és végtermékek
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fffb52e333bea1b2be11b127a9eab6656dc1d1f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94740328"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>A csoportos adatelemzési folyamat adatgyűjtési és-megértési szakasza

Ez a cikk a csoportos adatelemzési folyamat (TDSP) adatgyűjtési és-megértési szakaszával kapcsolatos célokat, feladatokat és teljesítéseket ismerteti. Ez a folyamat egy javasolt életciklust biztosít, amely segítségével strukturálhatja az adatelemzési projekteket. Az életciklus a projektek jellemzően végrehajtandó főbb szakaszait vázolja fel, gyakran iteratív:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

Itt látható a TDSP életciklus vizuális ábrázolása: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Olyan tiszta, magas színvonalú adatkészletet hoz létre, amelynek a megcélzott változókkal való kapcsolata megértette. Keresse meg az adatkészletet a megfelelő elemzési környezetben, hogy készen álljon a modellre.
* Fejlesszen ki egy olyan megoldási architektúrát, amely frissíti az adatfolyamatot, és rendszeres időközönként kiértékeli az adatmennyiséget.

## <a name="how-to-do-it"></a>Útmutató
Ebben a szakaszban három fő feladat foglalkozik:

   * **Az adatgyűjtést** a cél elemzési környezetbe.
   * **Fedezze fel az** adatmennyiséget, és állapítsa meg, hogy az adatminőség megfelelő-e a kérdés megválaszolásához. 
   * **Állítson be egy adatfolyamatot** az új vagy rendszeresen frissített adatértékek kiértékeléséhez.

### <a name="ingest-the-data"></a>Az adatfeldolgozás
Állítsa be, hogy az adatok a forrás helyeiről a célhelyre legyenek áthelyezve az elemzési műveletek, például a képzés és az előrejelzések futtatásához. Az adatok különböző Azure-adatszolgáltatásokkal való áthelyezésével kapcsolatos technikai részletekért lásd: [adatok betöltése tárolási környezetbe elemzés céljából](ingest-data.md). 

### <a name="explore-the-data"></a>Az adatok feltárása
A modellek betanítása előtt ki kell alakítania az adatelemzést. A valós adathalmazok gyakran zajosak, hiányoznak az értékek, vagy más eltéréseket tartalmaznak. Az adatösszesítés és a vizualizáció használatával naplózhatja az adatok minőségét, és megadhatja az adatok feldolgozásához szükséges adatokat, mielőtt készen áll a modellezésre. Ez a folyamat gyakran ismétlődő. Az adatok tisztításával kapcsolatos útmutatásért lásd: [feladatok az adatok előkészítéséhez a továbbfejlesztett gépi tanuláshoz](prepare-data.md).  

Miután elégedett a megtisztított adatmennyiség minőségével, a következő lépés az, hogy jobban megértse az adatelemekben rejlő mintákat. Ez az adatelemzés segít a célnak megfelelő prediktív modell kiválasztásában és fejlesztésében. Keresse meg azt a bizonyítékot, hogy az adatok mennyire jól kapcsolódnak a célhoz. Ezután állapítsa meg, hogy van-e elegendő mennyiségű adattal a következő modellezési lépésekkel való előrelépéshez. Ez a folyamat többször is ismétlődő. Előfordulhat, hogy az előző fázisban eredetileg azonosított adatkészletek kibővítéséhez az új adatforrások pontosabb vagy további releváns adatokat kell keresnie. 

### <a name="set-up-a-data-pipeline"></a>Adatfolyamat beállítása
Az adatok kezdeti betöltése és tisztítása mellett általában egy olyan folyamatot kell beállítania, amely új adatokkal szerzi be vagy rendszeresen frissíti az adatmennyiséget egy folyamatban lévő tanulási folyamat részeként. A pontozás egy adatfolyamattal vagy munkafolyamattal is elvégezhető. Az [adatok áthelyezése egy SQL Server-példányból a Azure SQL Databaseba Azure Data Factory](move-sql-azure-adf.md) cikkből megtudhatja, hogyan állíthat be egy folyamatot [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)használatával. 

Ebben a szakaszban fejleszti az adatfolyamatok megoldási architektúráját. Az adatelemzési projekt következő szakaszába párhuzamosan fejlesztheti a folyamatot. Az üzleti igényektől és a meglévő rendszerek korlátaitól függően, amelyekben a megoldás integrálva van, a folyamat az alábbi lehetőségek egyike lehet: 

   * Batch-alapú
   * Folyamatos átvitel vagy valós idejű 
   * Hibrid 

## <a name="artifacts"></a>Artifacts
Ebben a szakaszban a következő termékek szerepelnek:

   * [Adatminőségi jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Ez a jelentés az adatösszegzéseket, az egyes attribútumok és a cél, a változó rangsorolás és egyéb kapcsolatok közötti kapcsolatokat tartalmazza. 
   * **Megoldás-architektúra**: a megoldás architektúrája lehet az adatfolyamatok egy diagramja vagy leírása, amellyel a modell létrehozása után az új adatsorokra vonatkozó pontozási vagy előrejelzések futtathatók. Emellett tartalmazza azt a folyamatot is, amely új adatai alapján újratanítja a modellt. Tárolja a dokumentumot a [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) könyvtárában a TDSP Directory-struktúra sablonjának használatakor.
   * **Ellenőrzőpont-döntés**: Mielőtt megkezdené a teljes körű fejlesztést és a modell kialakítását, a projekt újraértékelésével megállapíthatja, hogy a várt érték elegendő-e a folytatáshoz. Előfordulhat például, hogy készen áll a folytatásra, további adatokat kell gyűjtenie, vagy fel kell vennie a projektet, mert az adatok nem léteznek a kérdés megválaszolására.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának egyes lépéseire mutató hivatkozásokat talál:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. Az útmutató bemutatja, hogyan egyesítheti a felhőt, a helyszíni eszközöket és a szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával]().
