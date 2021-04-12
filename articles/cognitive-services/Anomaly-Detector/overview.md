---
title: Mi az az Anomaly Detector API?
titleSuffix: Azure Cognitive Services
description: Az anomáliák Kiderítő API algoritmusai segítségével alkalmazhatja a anomáliák észlelését az idősorozat-adatain.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: anomáliák észlelése, gépi tanulás, algoritmusok
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 3bfa30cc5d3d57cfcd2677bb4b2719061541d5a9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278864"
---
# <a name="what-is-the-anomaly-detector-api"></a>Mi az az Anomaly Detector API?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A rendellenesség-Kiderítő API lehetővé teszi, hogy a gépi tanulás ismerete nélkül figyelje és azonosítsa az idősoros adataiban felmerülő rendellenességeket. A rendellenesség-érzékelő API algoritmusai úgy vannak kialakítva, hogy automatikusan azonosítják és alkalmazzák a legjobban illeszkedő modelleket az adataihoz, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől. Az idősorozat-adatok használatával az API meghatározza a rendellenességek észlelésére, a várt értékekre vonatkozó határokat, és hogy mely adatpontok rendellenesek.

![A szolgáltatási kérelmekben szereplő minták változásainak észlelése](./media/anomaly_detection2.png)

Az anomália detektor használata nem igényel korábbi felhasználói élményt a gépi tanulásban, és a REST API lehetővé teszi a szolgáltatás egyszerű integrálását az alkalmazásokba és folyamataiba.

Ez a dokumentáció a következő típusú cikkeket tartalmazza:
* A [rövid](./Quickstarts/client-libraries.md) útmutatók részletes útmutatást tesznek lehetővé, amelyekkel hívásokat indíthat a szolgáltatásba, és rövid idő alatt lekérheti az eredményeket. 
* A [útmutató útmutatók](./how-to/identify-anomalies.md) a szolgáltatás használatára vonatkozó utasításokat tartalmaznak részletesebb vagy testreszabott módokon.
* A [fogalmi cikkek](./concepts/anomaly-detection-best-practices.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.
* Az [oktatóanyagok](./tutorials/batch-anomaly-detection-powerbi.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja ezt a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.

## <a name="features"></a>Funkciók

Az anomáliák detektorral automatikusan észlelheti a rendellenességeket az idősorozat-adataiban, vagy valós időben végezheti el azokat.

|Szolgáltatás  |Leírás  |
|---------|---------|
|Anomáliák észlelése valós időben. | A korábban látott adatpontok használatával észlelheti a folyamatos adatátviteli hibák észlelését, és meghatározhatja, hogy a legújabb-e anomália. A művelet létrehoz egy modellt a küldött adatpontok használatával, és meghatározza, hogy a célként megadott pont anomáli-e. Ha az API-t az Ön által létrehozott összes új adatpontra hívja fel, az adatait a létrehozásuk során figyelheti. |
|Az adatkészletekben lévő rendellenességek észlelése kötegként. | Az idősorozat használatával észlelheti az összes olyan rendellenességet, amely létezhet az összes adategységben. A művelet létrehoz egy modellt a teljes idősorozat-adataival, és minden olyan pontot, amely ugyanazzal a modellel lett elemezve.         |
|A módosítási pontok észlelése az adatkészletben kötegként. | Az idősorozat használatával észlelheti az adataiban található trend-változási pontokat. A művelet létrehoz egy modellt a teljes idősorozat-adataival, és minden olyan pontot, amely ugyanazzal a modellel lett elemezve.    |
| További információkat kaphat az adatairól. | Hasznos információkhoz juthat az adatokról és az észlelt rendellenességekről, beleértve a várt értékeket, a rendellenességek határait és a pozíciókat. |
| Állítsa be a anomália észlelési határait. | Az anomália detektor API automatikusan létrehoz határokat a anomáliák észleléséhez. Állítsa be ezeket a határokat az API-k adatrendellenességekre való érzékenységének növeléséhez vagy csökkentéséhez, és jobban illeszkedjen az adataihoz. |

## <a name="demo"></a>Bemutató

Tekintse meg ezt az [interaktív bemutatót](https://aka.ms/adDemo) , hogy megtudja, hogyan működik a rendellenesség-érzékelő.
A bemutató futtatásához létre kell hoznia egy anomália-detektor erőforrást, és le kell kérnie az API-kulcsot és a végpontot.

## <a name="notebook"></a>Jegyzetfüzet

Ha meg szeretné tudni, hogyan hívhatja meg a rendellenesség-Kiderítő API-t, próbálkozzon a [jegyzetfüzettel](https://aka.ms/adNotebook). Ez a Jupyter Notebook bemutatja, hogyan küldhet API-kéréseket, és hogyan jelenítheti meg az eredményt.

A jegyzetfüzet futtatásához hajtsa végre a következő lépéseket:

1. Szerezzen be egy érvényes anomália-detektor API-előfizetési kulcsot és egy API-végpontot. Az alábbi szakasz útmutatást nyújt a regisztráláshoz.
1. Jelentkezzen be, majd a jobb felső sarokban kattintson a klónozás elemre.
1. A klónozási művelet befejezése előtt törölje a "nyilvános" lehetőséget a párbeszédpanelen, ellenkező esetben a notebook, beleértve az előfizetési kulcsokat is, nyilvános lesz.
1. Válassza **a Futtatás ingyenes számításkor** lehetőséget.
1. Válassza ki az egyik jegyzetfüzetet.
1. Adja hozzá az érvényes anomália-érzékelő API-előfizetési kulcsát a `subscription_key` változóhoz.
1. Módosítsa a `endpoint` változót a végpontra. Például: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. A felső menüsorban válassza a **cella**, majd az **összes futtatása** lehetőséget.

## <a name="workflow"></a>Munkafolyamat

Az anomália-detektor API egy REST-alapú webszolgáltatás, amely megkönnyíti a HTTP-kérelmeket és a JSON-elemzést elvégező programozási nyelv meghívását.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Regisztráció után:

1. Készítsen idősoros adatait, és alakítsa át érvényes JSON formátumra. Az adatelőkészítés során [ajánlott eljárásokat](concepts/anomaly-detection-best-practices.md) használhat a legjobb eredmények eléréséhez.
1. Küldjön egy kérelmet a rendellenesség-Kiderítő API-nak az adataival.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="algorithms"></a>Algoritmusok

* A használt algoritmusokról a következő technikai blogokban tájékozódhat:
    * [Az Azure Anomaly Detector API bemutatása](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Az SR-CNN algoritmus áttekintése az Azure-beli anomáliák Detektorában](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Ha többet szeretne megtudni a Microsoft által fejlesztett SR-CNN-algoritmusokról, olvassa el a Microsoft (KDD 2019 által elfogadott) [Time-sorozati anomália-észlelési szolgáltatást](https://arxiv.org/abs/1906.03821) .

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>Szolgáltatás rendelkezésre állása és redundancia

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>A rendellenesség-Kiderítő szolgáltatási zóna rugalmas?

Igen. A rendellenesség-Kiderítő szolgáltatás alapértelmezés szerint zónához rugalmas.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Hogyan úgy konfigurálja az anomália-detektor szolgáltatást, hogy a zóna rugalmas legyen?

A zóna rugalmasságának engedélyezéséhez nincs szükség ügyfél-konfigurációra. Az adatkezelési felderítő erőforrások rugalmassága alapértelmezés szerint elérhető, és maga a szolgáltatás kezeli.

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

Az API-funkciók helyszíni üzembe helyezéséhez [használjon anomáliát érzékelő tárolók használatával](anomaly-detector-container-howto.md) . A Docker-tárolók lehetővé teszik, hogy a szolgáltatás a megfelelőségi, biztonsági vagy egyéb működési okokból közelebb kerüljön az adataihoz.

## <a name="join-the-anomaly-detector-community"></a>Csatlakozás az Anomáliadetektor-közösséghez

* Csatlakozás az [anomália-detektor Advisors csoportjához a Microsoft Teams](https://aka.ms/AdAdvisorsJoin) szolgáltatásban
* A kiválasztott [felhasználó által létrehozott tartalom](user-generated-content.md) megjelenítése

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: anomáliák észlelése az idősoros adataiban az anomália detektor használatával](quickstarts/client-libraries.md)
* Az anomália érzékelő API [online bemutatója](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Az anomália detektor [REST API referenciája](https://aka.ms/anomaly-detector-rest-api-ref)
