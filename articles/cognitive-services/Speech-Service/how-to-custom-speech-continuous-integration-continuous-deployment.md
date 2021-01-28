---
title: CI/CD a Custom Speech-Speech szolgáltatáshoz
titleSuffix: Azure Cognitive Services
description: DevOps alkalmazása Custom Speech és CI/CD munkafolyamatokkal. Meglévő DevOps-megoldás implementálása a saját projekthez.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f7f11ef097d3abee2b4c18c32a1cb215d9fe8ce8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939895"
---
# <a name="cicd-for-custom-speech"></a>CI/CD a Custom Speechhez

Az automatizált betanítási, tesztelési és kiadási felügyelet megvalósítása lehetővé teszi a Custom Speech-modellek folyamatos fejlesztését, miközben a frissítéseket alkalmazza a képzési és tesztelési célokra. A CI/CD-munkafolyamatok hatékony megvalósításával gondoskodhat arról, hogy a legjobb teljesítményű Custom Speech modell végpontja mindig elérhető legyen.

A [folyamatos integráció](/azure/devops/learn/what-is-continuous-integration) (CI) olyan mérnöki gyakorlat, amely egy megosztott tárházban gyakran véglegesíti a frissítéseket, és automatizált felépítést végez rajta. A CI-munkafolyamatok a Custom Speech betanítják az új modellt az adatforrásaiból, és automatizált tesztelést végeznek az új modellen, így biztosítva, hogy az a korábbi modellnél jobb teljesítményt nyújt.

A [folyamatos teljesítés](/azure/devops/learn/what-is-continuous-delivery) (CD) a CI folyamat modelljeit veszi alapul, és minden továbbfejlesztett Custom Speech modellhez létrehoz egy végpontot. A CD lehetővé teszi, hogy a végpontok könnyen elérhetők legyenek a megoldásokba való integráláshoz.

Az egyéni CI/CD-megoldások lehetségesek, de robusztus, előre elkészített megoldásként használja a [Speech DevOps-sablon tárházát](https://github.com/Azure-Samples/Speech-Service-DevOps-Template), amely a GitHub-műveletek használatával hajtja végre a CI/CD-munkafolyamatokat.

## <a name="cicd-workflows-for-custom-speech"></a>CI/CD-munkafolyamatok Custom Speechhoz

A munkafolyamatok célja annak biztosítása, hogy minden Custom Speech modell jobb felismerési pontossággal rendelkezik, mint az előző Build. Ha a tesztelési és/vagy betanítási adatok frissítései javítják a pontosságot, a munkafolyamatok új Custom Speech végpontot hoznak létre.

A git-kiszolgálók, például a GitHub és az Azure DevOps automatizált munkafolyamatokat futtathatnak, amikor adott git-események történnek, például egyesítések vagy lekéréses kérelmek. A CI-munkafolyamatok például akkor indíthatók el, ha a tesztelési célú adatfrissítéseket a *fő* ágra küldi. A különböző git-kiszolgálók eltérő eszközökkel fognak rendelkezni, de lehetővé teszik a parancssori felületi (CLI) parancsok futtatását, hogy azok egy Build-kiszolgálón futtathatók legyenek.

Így a munkafolyamatoknak az adatok, tesztek, tesztelési fájlok, modellek és végpontok nevét és tárolását kell megadniuk, hogy azok visszavezethetők legyenek a véglegesített vagy verzióra. Emellett hasznos lehet ezeknek az objektumoknak a megadnia, hogy könnyen láthatók legyenek, melyeket a tesztelési adatok és a betanítási adatok frissítése után hoztak létre.

### <a name="ci-workflow-for-testing-data-updates"></a>CI-munkafolyamat az adatfrissítések teszteléséhez

A CI/CD-munkafolyamatok elsődleges célja, hogy egy új modellt hozzon létre a betanítási adatok használatával, és tesztelje a modellt a tesztelési adatok használatával annak megállapításához, hogy a [Word Error Rate](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) a korábbi legjobban teljesítő modellhez képest javult-e (a "teljesítményteszt-modell"). Ha az új modell jobban teljesít, ez lesz az új teljesítményteszt-modell, amely a jövőbeli modelleket hasonlítja össze.

Az adatfrissítések tesztelésére szolgáló CI-munkafolyamatnak újra kell tesztelni a jelenlegi teljesítményteszt-modellt a frissített tesztelési adattal a felülvizsgált WER kiszámításához. Ez biztosítja, hogy amikor egy új modell WERjét összehasonlítja a teljesítményteszt WER-modelljével, mindkét modellt ugyanazzal a tesztelési adattal tesztelték, és a hasonló módon hasonlítjuk össze.

Ennek a munkafolyamatnak aktiválnia kell a tesztelési célú és a következő frissítéseket:

- Tesztelje a teljesítményteszt modelljét a frissített tesztelési adatértékekkel.
- Tárolja a teszt kimenetét, amely tartalmazza a teljesítményteszt-modellt a frissített adatokat használva.
- Ezekből a tesztekből származó WER lesz az új teljesítményteszt, amelyet a jövőbeli modelleknek meg kell verniük.
- A CD-munkafolyamat nem hajt végre frissítéseket a tesztelési célú frissítésekhez.

### <a name="ci-workflow-for-training-data-updates"></a>CI-munkafolyamat a betanítási adatfrissítésekhez

A betanítási információk frissítései az egyéni modell frissítéseire mutatnak.

Ennek a munkafolyamatnak aktiválnia kell a betanítási információk frissítéseit, és:

- Új modell betanítása a frissített betanítási adatszolgáltatással.
- Tesztelje az új modellt a tesztelési adatként.
- Tárolja a teszt kimenetét, amely tartalmazza a WER-t.
- Hasonlítsa össze a WER-t az új modellből a WER-be a benchmark modellből.
- Ha a WER nem javítja a folyamatot, állítsa le a munkafolyamatot.
- Ha a WER javítja, hajtsa végre a CD-munkafolyamatot Custom Speech végpont létrehozásához.

### <a name="cd-workflow"></a>CD-munkafolyamat

Miután a betanítási adatgyűjtést javító frissítést végez, a CD-munkafolyamatnak automatikusan végre kell hajtania egy új végpont létrehozását a modellhez, és a végpontot elérhetővé kell tennie, hogy az használható legyen a megoldásban.

#### <a name="release-management"></a>Kiadáskezelés

A legtöbb csapatnak manuális felülvizsgálati és jóváhagyási folyamatra van szüksége az éles környezetbe történő üzembe helyezéshez. Éles környezetben érdemes lehet meggyőződni arról, hogy a fejlesztői csapat kulcsfontosságú tagjai támogatásra vagy alacsony forgalmú időszakokra vonatkozóan állnak rendelkezésre.

### <a name="tools-for-custom-speech-workflows"></a>Eszközök Custom Speech munkafolyamatokhoz

A következő eszközök használhatók a Custom Speech CI/CD Automation munkafolyamataihoz:

- Azure [CLI](/cli/azure/) az Azure-szolgáltatás egyszerű hitelesítésének létrehozásához, az Azure-előfizetések lekérdezéséhez és a tesztelési eredmények tárolásához az Azure blobban.
- Az [Azure SPEECH CLI](spx-overview.md) segítségével a parancssorból vagy egy automatizált munkafolyamatból dolgozhat a Speech szolgáltatással.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>DevOps-megoldás a Custom Speech GitHub-műveletek használatával

A Custom Speechhez már megvalósított DevOps-megoldáshoz nyissa meg a [Speech DevOps-sablon](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)tárházát. Hozzon létre egy másolatot a sablonról, és kezdje meg az egyéni modellek fejlesztését egy olyan robusztus DevOps rendszerrel, amely tartalmazza a tesztelést, a képzést és a verziószámozást a GitHub-műveletek használatával. A tárház tesztelési és betanítási információkkal segíti a telepítést, és ismerteti a munkafolyamatot. A kezdeti beállítás után cserélje le a mintaadatok a Project adataira.

A [beszédfelismerési DevOps-sablon](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) a következőkhöz nyújt infrastruktúrát és részletes útmutatást:

- Másolja a sablon tárházát a GitHub-fiókjába, majd hozzon létre Azure-erőforrásokat és egy [egyszerű szolgáltatásnevet](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) a GitHub-műveletek CI/CD-munkafolyamataihoz.
- Végigvezeti a "[dev Inner loop](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)"-t. Frissítheti a betanítási és tesztelési adatait egy adott szolgáltatási ágra, tesztelheti a módosításokat egy ideiglenes fejlesztési modellel, és lekéréses kérelmet hozhat létre, és áttekintheti a módosításokat.
- Ha a betanítási kérelmeket egy lekéréses kérelemben frissítik a *Main*-ba, a modelleket a GitHub ACTIONs CI-munkafolyamattal.
- Automatizált pontossági tesztelést végezhet a modell [Word-hibák arányának](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) létrehozásához. A teszt eredményeinek tárolása az Azure blobban.
- Futtassa a CD-munkafolyamatot, és hozzon létre egy végpontot, amikor a WER fejleszti.

## <a name="next-steps"></a>Következő lépések

További információ a DevOps és a Speech:

- Használja a [Speech DevOps-sablon](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) adattárat a GitHub-műveletekkel rendelkező Custom Speech DevOps megvalósításához.
