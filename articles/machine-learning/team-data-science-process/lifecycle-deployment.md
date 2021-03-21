---
title: A csoportos adatelemzési folyamat életciklusának üzembe helyezési szakasza
description: Az adattudományi projektek üzembe helyezési szakaszának célja, feladatai és termékei
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
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324548"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusának üzembe helyezési szakasza

Ez a cikk a csoportos adatelemzési folyamat (TDSP) üzembe helyezéséhez kapcsolódó célokat, feladatokat és teljesítéseket ismerteti. Ez a folyamat egy javasolt életciklust biztosít, amely segítségével strukturálhatja az adatelemzési projekteket. Az életciklus a projektek jellemzően végrehajtandó főbb szakaszait vázolja fel, gyakran iteratív:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

Itt látható a TDSP életciklus vizuális ábrázolása: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
Modellek üzembe helyezése adatfolyamattal éles vagy éles környezetben, a végfelhasználói elfogadás érdekében. 

## <a name="how-to-do-it"></a>Útmutató
Az ebben a szakaszban tárgyalt fő feladat:

**Működővé tenni**: a modell és a folyamat üzembe helyezése éles vagy éles környezetben az alkalmazások felhasználásához.

### <a name="operationalize-a-model"></a>Modell üzembe helyezése
Miután jól teljesített modelleket tartalmazó készletet használ, működővé tenni azokat más alkalmazások számára. Az üzleti követelményektől függően a jóslatok valós időben vagy batch alapon történnek. A modellek üzembe helyezéséhez egy nyitott API-felülettel kell kitenni őket. Az interfész lehetővé teszi, hogy a modell könnyen felhasználható legyen különböző alkalmazásokból, például:

   * Online webhelyek
   * Táblázatok 
   * Irányítópultok
   * Üzletági alkalmazások 
   * Háttérbeli alkalmazások 

Azure Machine Learning webszolgáltatással történő operacionalizálási kapcsolatos példákért tekintse meg a [Azure Machine learning webszolgáltatás üzembe helyezése](../classic/deploy-a-machine-learning-web-service.md)című témakört. Az ajánlott eljárás az üzemi modellbe való telemetria és monitorozás, valamint az üzembe helyezett adatfolyamatok létrehozása. Ez a gyakorlat segítséget nyújt a rendszerállapot-jelentéskészítés és a hibaelhárítás során.  

## <a name="artifacts"></a>Artifacts

* Állapot-irányítópult, amely a rendszerállapotot és a kulcs metrikáit jeleníti meg
* Végső modellezési jelentés az üzembe helyezés részleteivel
* Az utolsó megoldás architektúrájának dokumentuma


## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának egyes lépéseire mutató hivatkozásokat talál:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és-megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. Az útmutató bemutatja, hogyan egyesítheti a felhőt, a helyszíni eszközöket és a szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](./index.yml).