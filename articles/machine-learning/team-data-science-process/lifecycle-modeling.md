---
title: A csoportos adatelemzési folyamat életciklusának modellezési szakasza
description: Az adatelemzési projektek modellezési fázisának céljai, feladatai és termékei
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
ms.openlocfilehash: 4719bd21bc6299373f9dabfe4733541c6fa27b94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94740181"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusának modellezési szakasza

Ez a cikk a csoportos adatelemzési folyamat (TDSP) modellezési szakaszához kapcsolódó célokat, feladatokat és teljesítéseket ismerteti. Ez a folyamat egy javasolt életciklust biztosít, amely segítségével strukturálhatja az adatelemzési projekteket. Az életciklus a projektek jellemzően végrehajtandó főbb szakaszait vázolja fel, gyakran iteratív:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

Itt látható a TDSP életciklus vizuális ábrázolása:

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Határozza meg a gépi tanulási modell optimális adatszolgáltatásait.
* Hozzon létre egy tájékoztató gépi tanulási modellt, amely pontosan megjósolja a célt.
* Hozzon létre egy gépi tanulási modellt, amely éles környezetben használható.

## <a name="how-to-do-it"></a>Útmutató
Ebben a szakaszban három fő feladat foglalkozik:

  * **Szolgáltatás-mérnöki**: adatszolgáltatások létrehozása a nyers adatokból a modell betanításának elősegítése érdekében.
  * **Modell betanítása**: keresse meg a modellt, amely a lehető legpontosabban válaszol a sikerességi mérőszámok összehasonlításával.
  * Állapítsa meg, hogy a modell **alkalmas-e a gyártásra.**

### <a name="feature-engineering"></a>Jellemzőkiemelés
A szolgáltatások mérnöki funkciói közé tartozik a nyers változók bevonása, összesítése és átalakítása az elemzésben használt funkciók létrehozásához. Ha szeretné betekintést adni a modellre, akkor meg kell ismernie, hogy a funkciók hogyan kapcsolódnak egymáshoz, és hogy a gépi tanulási algoritmusok hogyan használják ezeket a funkciókat. 

Ehhez a lépéshez a tartományi szakértelmet és az adatfeltárási lépésből beszerzett elemzéseket kreatívan kell kombinálni. A funkciók mérnöki feladata a megtalálása és az informatív változók megkeresése, de ugyanakkor a túl sok nem kapcsolódó változó elkerülésére tett kísérlet. Informatív változók javítják az eredményt; a nem kapcsolódó változók szükségtelen zajt vezetnek be a modellbe. Ezeket a funkciókat a pontozás során beszerzett új adatszolgáltatásokhoz is meg kell hoznia. Ennek eredményeképpen ezeknek a szolgáltatásoknak a létrehozása csak a pontozás időpontjában elérhető adatoktól függ. 

A különböző Azure-adattechnológiákat használó funkciók mérnöki műszaki útmutatója: [az adatelemzési folyamat funkcióinak](create-features.md)fejlesztése. 

### <a name="model-training"></a>A modell betanítása
A választ igénylő kérdés típusától függően számos modellezési algoritmus érhető el. Az algoritmusok kiválasztásával kapcsolatos útmutatásért lásd: [algoritmusok kiválasztása Microsoft Azure Machine Learninghoz](../how-to-select-algorithms.md). Bár ez a cikk Azure Machine Learningt használ, az általa biztosított útmutatás minden gépi tanulási projekt esetében hasznos lehet. 

A modell betanításának folyamata a következő lépéseket tartalmazza: 

   * **A bemeneti adatok véletlenszerűen való felosztása** modellezési adatkészletbe és tesztelési adatkészletbe.
   * Hozza **létre a modelleket** a betanítási adatkészlet használatával.
   * **Értékelje** ki a betanítást és a tesztelési adatkészletet. A különböző társított hangolási paraméterekkel, valamint az aktuális adatokkal kapcsolatos kérdések megválaszolásával megválaszolva a versengő gépi tanulási algoritmusok sorozatát is használhatja.
   * **Határozza meg a "legjobb" megoldást** a kérdés megválaszolásához az alternatív módszerek közötti sikerességi mérőszámok összehasonlításával.

> [!NOTE]
> A **szivárgás elkerülése**: adatszivárgást okozhat, ha a betanítási adatkészleten kívülről származó adatokkal is rendelkezik, ami lehetővé teszi, hogy a modell vagy a gépi tanulási algoritmus reálisan jó előrejelzéseket készítsen. A szivárgás gyakori oka annak, hogy az adatelemzők miért idegesek, amikor olyan prediktív eredményeket kapnak, amelyek túl jól tűnnek a valóságnak. Ezek a függőségek nehezen észlelhetők. A szivárgás elkerüléséhez az Analysis-adatkészletek kiépítése, a modell létrehozása és az eredmények pontosságának kiértékelése között ismétlődő lépések szükségesek. 
> 
> 

## <a name="artifacts"></a>Artifacts
Az ebben a szakaszban bemutatott összetevők a következők:

   * [Szolgáltatások készletei](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): a modellezéshez kifejlesztett funkciók leírása az **adatdefiníciós** jelentés **szolgáltatáskészlet-készletek** szakaszában található. A kód mutatóit tartalmazza a funkciók létrehozásához és a szolgáltatás létrehozásának leírásához.
   * [Modell jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): minden olyan modell esetében, amely minden egyes kísérlethez meg van határozva, egy standard, sablon alapú jelentés készül, amely részletesen ismerteti az egyes kísérletek részleteit.
   * **Ellenőrzőpont-döntés**: annak kiértékelése, hogy a modell megfelelően működik-e az éles környezetben. Néhány fontos kérdés:
     * A modell megfelelő megbízhatósággal válaszol a kérdésre a tesztelési adat miatt? 
     * Érdemes kipróbálnia bármilyen alternatív megközelítést? További adatok gyűjtése, több funkció-mérnöki megoldás vagy más algoritmusok kipróbálása?

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának egyes lépéseire mutató hivatkozásokat talál:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes körű útmutatót biztosítunk, amely bemutatja a folyamat összes lépését adott forgatókönyvek esetén. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. Az útmutató bemutatja, hogyan egyesítheti a felhőt, a helyszíni eszközöket és a szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](./index.yml).
