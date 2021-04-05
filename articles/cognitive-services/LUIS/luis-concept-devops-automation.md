---
title: A LUIS-alkalmazások folyamatos munkafolyamatai
description: A CI/CD-munkafolyamatok megvalósítása a DevOps for Language Understanding (LUIS) számára.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932542"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Folyamatos integráció és folyamatos kézbesítési munkafolyamatok LUIS DevOps

A Language Understanding-(LUIS-) alkalmazást fejlesztő szoftverfejlesztők DevOps eljárásokat alkalmazhatnak a [verziókövetés](luis-concept-devops-sourcecontrol.md), az [automatizált buildek](luis-concept-devops-automation.md), a [tesztelés](luis-concept-devops-testing.md)és a [kiadás-felügyelet](luis-concept-devops-automation.md#release-management)köré. Ez a cikk a LUIS-hoz készült automatizált buildek megvalósításával kapcsolatos fogalmakat ismerteti.

## <a name="build-automation-workflows-for-luis"></a>Automatizálási munkafolyamatok létrehozása LUIS számára

![CI-munkafolyamatok](./media/luis-concept-devops-automation/luis-automation.png)

A forráskód-kezelési (SCM) rendszerekben konfigurálja az automatizált Build-folyamatokat, hogy azok a következő eseményeken fussanak:

1. [Lekéréses kérelem](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) kiváltásakor kiváltott **PR-munkafolyamat** . Ez a munkafolyamat ellenőrzi a PR tartalmát, *mielőtt* a frissítések beolvadnak a fő ágra.
1. A **CI/CD munkafolyamat** akkor aktiválódik, ha a frissítéseket a fő ágra küldi, például a módosítások egy PR-ból való egyesítése után. Ez a munkafolyamat biztosítja a fő ág frissítéseinek minőségét.

A **CI/CD munkafolyamat** két kiegészítő fejlesztési folyamatot egyesít:

* A [folyamatos integráció](/azure/devops/learn/what-is-continuous-integration) (CI) olyan mérnöki gyakorlat, amely egy megosztott tárházban gyakran véglegesíti a kódot, és automatizált felépítést végez rajta. Az automatizált [tesztelési](luis-concept-devops-testing.md) módszerekkel párosítva a folyamatos integráció lehetővé teszi, hogy minden frissítés esetében ellenőrizze, hogy az LUDown-forrás továbbra is érvényes-e, és hogy egy Luis-alkalmazásba importálható-e, de azt is, hogy a betanított alkalmazást a megoldáshoz szükséges szándékok és entitások azonosítására szolgáló tesztek egy csoportja adja át.

* A [folyamatos teljesítés](/azure/devops/learn/what-is-continuous-delivery) (CD) továbbra is a folyamatos integrációs koncepciót alkalmazza az alkalmazás olyan környezetbe való automatikus üzembe helyezéséhez, ahol alaposabb tesztelést végezhet. A CD lehetővé teszi, hogy a lehető leggyorsabban megismerje a változásokkal kapcsolatos előre nem látható problémákat, valamint a tesztelési lefedettség hiányosságainak megismerését is.

A folyamatos integráció és a folyamatos teljesítés célja annak biztosítása, hogy a "fő mindig shippable". A LUIS-alkalmazások esetében ez azt jelenti, hogy ha szükséges, a fő ág LUIS-alkalmazásból, és az éles környezetben szállítjuk.

### <a name="tools-for-building-automation-workflows-for-luis"></a>A LUIS automatizálási munkafolyamatainak létrehozásához szükséges eszközök

A Build Automation-munkafolyamatok létrehozásához különböző Build Automation-technológiák érhetők el. Mindegyiknek szüksége van arra, hogy a parancssori felület (CLI) vagy a REST-hívások használatával parancsfájlokat lehessen végrehajtani, hogy azok egy Build-kiszolgálón fussanak.

Az alábbi eszközöket használhatja a LUIS-alapú automatizálási munkafolyamatok létrehozásához:

* A [bot Framework Tools Luis CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) a Luis-alkalmazásokkal és-verziókkal,-kiképzéssel, teszteléssel és a Luis szolgáltatáson belüli közzétételével használható.

* Azure [CLI](/cli/azure/) az Azure-előfizetések lekérdezéséhez, a Luis Authoring and előrejelzési kulcsok beolvasásához, valamint az Automation-hitelesítéshez használt Azure- [szolgáltatás](/cli/azure/ad/sp) létrehozásához.

* [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) eszköz a [Luis-alkalmazások teszteléséhez](luis-concept-devops-testing.md) és a tesztek eredményeinek elemzéséhez.

### <a name="the-pr-workflow"></a>A PR-munkafolyamat

Ahogy azt korábban említettük, ezt a munkafolyamatot úgy állíthatja be, hogy akkor fusson, amikor egy fejlesztő egy PR-t terjeszt elő, hogy a szolgáltatás a fő ágra egyesítse a módosításokat. Ennek célja, hogy ellenőrizze a PR-változások minőségét, mielőtt egyesíteni tudnák a fő ágat.

Ennek a munkafolyamatnak a következőket kell tennie:

* Hozzon létre egy ideiglenes LUIS-alkalmazást úgy, hogy importálja a `.lu` forrást a PR-ban.
* A LUIS-alkalmazás verziójának betanítása és közzététele.
* Futtassa az összes [egységet a tesztek](luis-concept-devops-testing.md) ellen.
* Adja át a munkafolyamatot, ha az összes teszt át lett hajtva, ellenkező esetben a hibát.
* Törölje az ideiglenes alkalmazást.

Ha az SCM támogatja, konfigurálja a fiókirodák védelmi szabályait úgy, hogy a munkafolyamatot sikeresen végre lehessen hajtani a PR befejezése előtt.

### <a name="the-main-branch-cicd-workflow"></a>A fő ág CI/CD-munkafolyamata

Konfigurálja ezt a munkafolyamatot úgy, hogy az a folyamatban lévő frissítések a fő ágra való egyesítése után fusson. Ennek célja, hogy a frissítések tesztelésével a fő ág magas minőségi sávján maradjon. Ha a frissítések megfelelnek a minőségi sávra, ez a munkafolyamat telepíti az új LUIS-alkalmazás verzióját egy olyan környezetbe, ahol alaposabb tesztelést végezhet.

Ennek a munkafolyamatnak a következőket kell tennie:

* Hozzon létre egy új verziót az elsődleges LUIS-alkalmazásban (a fő ág számára fenntartott alkalmazás) a frissített forráskód használatával.

* A LUIS-alkalmazás verziójának betanítása és közzététele.

  > [!NOTE]
  > Ahogy azt a [tesztek futtatása egy automatizált Build-munkafolyamatban](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) kifejtette, a teszt alatt közzé kell tennie a Luis-alkalmazás verziószámát, hogy az eszközök, például a NLU. A DevOps elérheti. A LUIS csak két elnevezett közzétételi tárolóhelyet támogat, az *előkészítést* és a *gyártást* egy Luis-alkalmazáshoz, de [közvetlenül is közzéteheti a verziót](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) , és megtekintheti [a verziószámot.](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name) Használja az automatizálási munkafolyamatokban a közvetlen verziók közzétételét, hogy elkerülje, hogy az elnevezett közzétételi tárolóhelyek használatára korlátozódjon.

* Futtassa az összes [egység tesztet](luis-concept-devops-testing.md).

* Igény szerint futtathat [Batch-teszteket](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) a Luis-alkalmazás verziójának minőségének és pontosságának méréséhez, és összehasonlíthatja azt valamilyen alaptervtel.

* Ha a tesztek sikeresen befejeződik:
  * Címkézze fel a forrást a tárházban.
  * Futtassa a folyamatos kézbesítés (CD) feladatot a LUIS-alkalmazás verziójának környezetekben történő üzembe helyezéséhez további tesztelés céljából.

### <a name="continuous-delivery-cd"></a>Folyamatos teljesítés (CD)

A CI/CD-munkafolyamatokban a CD-feladatok feltételesen a build és az automatizált egység tesztek sikerén futnak. Feladata, hogy automatikusan telepítse a LUIS alkalmazást egy olyan környezetre, ahol további tesztelést végezhet.

A LUIS-alkalmazás üzembe helyezéséhez a legjobb megoldás, ha a projekthez megfelelő folyamatot kell megvalósítani. A [Luis DevOps-sablon](https://github.com/Azure-Samples/LUIS-DevOps-Template) tárháza egy egyszerű megoldást valósít meg, amely az [új Luis-alkalmazás verziójának közzététele](./luis-how-to-publish-app.md) az *éles* közzétételi tárolóhelyen. Ez egy egyszerű beállítás. Ha azonban számos különböző éles környezetben működő környezetet kell támogatnia, mint például a *fejlesztés*, az *előkészítés* és a *ellenőrzését*, akkor az alkalmazáson belül a két elnevezett közzétételi tárolóhely korlátja elégtelennek bizonyul.

Az alkalmazások verziójának központi telepítésének egyéb lehetőségei a következők:

* Hagyja meg az alkalmazás verziószámát a közvetlen verzió végpontján, és hajtson végre egy folyamatot, amely az alsóbb rétegbeli éles környezeteket szükség szerint konfigurálja a közvetlen verziójú végponttal.
* Az egyes éles környezetekben különböző LUIS-alkalmazásokat tarthat fenn, és írhat automatizálási lépéseket az új verzióra történő importálásához a `.lu` Luis alkalmazásban a cél éles környezetben, a betanításhoz és a közzétételhez.
* Exportálja a tesztelt LUIS-alkalmazás verzióját egy [Luis Docker-tárolóba](./luis-container-howto.md?tabs=v3) , és telepítse a Luis-tárolót az Azure [Container instances](../../container-instances/index.yml)szolgáltatásba.

## <a name="release-management"></a>Kiadáskezelés

Általában azt javasoljuk, hogy csak a nem éles környezetekhez, például a fejlesztéshez és az előkészítéshez végezzen folyamatos teljesítést. A legtöbb csapatnak manuális felülvizsgálati és jóváhagyási folyamatra van szüksége az éles környezetbe történő üzembe helyezéshez. Éles környezetben érdemes lehet meggyőződni arról, hogy a fejlesztői csapat kulcsfontosságú tagjai támogatásra vagy alacsony forgalmú időszakokra vonatkozóan állnak rendelkezésre.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan valósítható meg a [DevOps for Luis a GitHub](luis-how-to-devops-with-github.md) használatával
* Megtudhatja, hogyan írhat egy [GitHub-műveletek munkafolyamatot a NLU. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
