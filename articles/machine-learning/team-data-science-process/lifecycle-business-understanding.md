---
title: Üzleti ismeretek a csoportos adatelemzési folyamatban
description: A csoportos adatelemzési folyamat során az adatelemzési projektek üzleti megértéséhez szükséges célok, feladatok és termékek.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "76710332"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusának üzleti megértési szakasza

Ez a cikk a csoportos adatelemzési folyamat (TDSP) üzleti megértési szakaszával kapcsolatos célokat, feladatokat és teljesítéseket ismerteti. Ez a folyamat egy javasolt életciklust biztosít, amely segítségével strukturálhatja az adatelemzési projekteket. Az életciklus a projektek jellemzően végrehajtandó főbb szakaszait vázolja fel, gyakran iteratív:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

Itt látható a TDSP életciklus vizuális ábrázolása: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Adja meg azokat a fő változókat, amelyek a modell céljaként szolgálnak, és amelyek a kapcsolódó mérőszámokat használják a projekt sikerességének meghatározásához.
* Azonosítsa azokat a releváns adatforrásokat, amelyekhez a vállalat hozzáfér, vagy amelyet meg kell szereznie.

## <a name="how-to-do-it"></a>Útmutató
Ebben a szakaszban két fő feladat foglalkozik: 

   * **Célkitűzések meghatározása**: az üzleti problémák megismeréséhez és azonosításához használja az ügyfelet és más érdekelteket. Olyan kérdéseket fogalmazhat meg, amelyek meghatározzák az adatelemzési technikák által megcélzott üzleti célokat.
   * **Adatforrások azonosítása**: keresse meg a releváns adatokat, amelyek segítségével választ kaphat a projekt céljait meghatározó kérdésekre.

### <a name="define-objectives"></a>Célkitűzések meghatározása
1. Ennek a lépésnek a központi célja, hogy azonosítsa azokat a kulcsfontosságú üzleti változókat, amelyeknek meg kell jósolnia az elemzést. Ezeket a változókat a *modell céljaként* tekintjük át, és a hozzájuk társított metrikákat használjuk a projekt sikerességének meghatározásához. Az ilyen célok két példája az értékesítési előrejelzések vagy a megrendelés csalárd valószínűsége.

2. A projekt céljainak meghatározása: az "éles" kérdésekkel kapcsolatos, konkrét és kétértelmű kérdések megkérdezése és finomítása. Az adatelemzés olyan folyamat, amely neveket és számokat használ az ilyen kérdések megválaszolásához. Az adatelemzés és a gépi tanulás jellemzően öt típusú kérdés megválaszolására használható:
 
   * Mennyit vagy hányat? regressziós
   * Melyik kategória? besorolási
   * Melyik csoport? fürtszolgáltatás
   * Ez fura? (anomáliák észlelése)
   * Melyik beállítást kell figyelembe venni? ajánlás

   Határozza meg, hogy mely kérdésekre kéri fel, és hogy milyen módon érheti el üzleti céljait.

3. Adja meg a projekt csapatát a tagjai szerepköreinek és feladatainak megadásával. Dolgozzon ki egy magas szintű mérföldkő-tervet, amelyről részletesebben is tájékozódhat. 

4. Adja meg a sikerességi metrikákat. Előfordulhat például, hogy az ügyfél-adatforgalom előrejelzését szeretné elérni. A három hónapos projekt végén a "x" százalék pontossága szükséges. Ezekkel az adatforgalommal csökkentheti az ügyfelek akcióit. A metrikáknak **intelligensnek** kell lenniük: 

   * **S** pecific 
   * **M** easurable
   * **Egy** chievable 
   * **R**-elevant 
   * **T** IME – kötve 

### <a name="identify-data-sources"></a>Adatforrások azonosítása
Azonosítsa azokat az adatforrásokat, amelyek ismert példákat tartalmaznak az éles kérdések megválaszolására. Keresse meg a következő adatértékeket:

* A kérdéshez kapcsolódó adatokat. Van-e a célhoz kapcsolódó célkitűzésekkel és szolgáltatásokkal kapcsolatos mértéke?
* A modell céljának pontos mértékét és a fontos funkciókat tartalmazó adatmennyiség.

Előfordulhat például, hogy a meglévő rendszereknek további típusú adatokat kell összegyűjteniük és naplóznia a probléma megoldásához és a projekt céljainak eléréséhez. Ebben az esetben előfordulhat, hogy külső adatforrásokat szeretne keresni, vagy frissítenie kell a rendszereket az új adatok gyűjtéséhez.

## <a name="artifacts"></a>Artifacts
Ebben a szakaszban a következő termékek szerepelnek:

   * [Charter-dokumentum](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): a TDSP-projekt szerkezetének definíciójában szabványos sablon szerepel. A charter dokumentum egy élő dokumentum. A sablon frissítése a projekt során az új felfedezések és az üzleti követelmények változása révén végezhető el. A legfontosabb, hogy megismételje a dokumentumot, és részletesebben adja meg a felderítési folyamat előrehaladását. Tartsa meg az ügyfelet és a többi érintett felet a módosítások elvégzésében, és egyértelműen tájékoztassa a módosításokat.  
   * [Adatforrások](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): a TDSP Project **ADATJELENTÉSI** mappájában található **adatdefiníciós** jelentés **nyers adatforrások** szakasza tartalmazza az adatforrásokat. Ez a szakasz a nyers adatként használt eredeti és célhelyeket határozza meg. A későbbi fázisokban további részleteket adhat meg, például a szkripteket, amelyek az adatokat az analitikus környezetbe helyezik át.  
   * [Adatszótárak](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Ez a dokumentum az ügyfél által biztosított adatmennyiség leírását tartalmazza. Ezek a leírások tartalmazzák a sémával kapcsolatos információkat (az adattípusokat és az érvényesítési szabályokra vonatkozó adatokat, ha vannak ilyenek) és az entitás-relációs diagramokat, ha vannak ilyenek.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának egyes lépéseire mutató hivatkozásokat talál:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. Az útmutató bemutatja, hogyan egyesítheti a felhőt, a helyszíni eszközöket és a szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 
