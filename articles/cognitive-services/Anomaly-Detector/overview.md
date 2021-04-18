---
title: Mi az az Anomaly Detector API?
titleSuffix: Azure Cognitive Services
description: A anomáliadetektor API algoritmusai segítségével anomáliadetektálást alkalmazhat az idősorozat-adatokra.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: anomáliadetektálás, gépi tanulás, algoritmusok
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d63399d0f492f85a4a2d57a595a6d8ef5b606d92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599519"
---
# <a name="what-is-the-anomaly-detector-api"></a>Mi az az Anomaly Detector API?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A anomáliadetektor API lehetővé teszi az idősorozat-adatok rendellenességének figyelését és észlelését anélkül, hogy gépi tanulásra volna képes. A anomáliadetektor API algoritmusai úgy alkalmazkodnak, hogy automatikusan azonosítják és alkalmazzák az adatokhoz leginkább illeszkedő modelleket, az iparágtól, a forgatókönyvtől és az adatmennyiségtől függetlenül. Az idősorozat-adatok használatával az API meghatározza az anomáliadetektálás határait, a várt értékeket és az adatpontok anomáliákat.

![Szolgáltatáskérések mintázatváltozásának észlelése](./media/anomaly_detection2.png)

A anomáliadetektor használata nem igényel korábbi gépi tanulási tapasztalatot, a RESTful API pedig lehetővé teszi a szolgáltatás egyszerű integrálását az alkalmazásokba és folyamatokba.

Ez a dokumentáció a következő cikktípusokat tartalmazza:
* A [rövid útmutatók olyan](./Quickstarts/client-libraries.md) részletes utasítások, amelyek segítségével rövid időn belül hívásokat kezdeményez a szolgáltatáshoz, és eredményeket kap. 
* Az [útmutatók a](./how-to/identify-anomalies.md) szolgáltatás pontosabb vagy testre szabottabb módon való használatával kapcsolatos utasításokat tartalmaznak.
* Az [elméleti cikkek](./concepts/anomaly-detection-best-practices.md) részletes magyarázatot nyújtanak a szolgáltatás funkcióiról és funkcióiról.
* Az [oktatóanyagok hosszabb](./tutorials/batch-anomaly-detection-powerbi.md) útmutatók, amelyekből megtudhatja, hogyan használhatja ezt a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.

## <a name="features"></a>Funkciók

A anomáliadetektor automatikusan észlelheti a rendellenességeket az idősorozat-adatokban, vagy valós időben.

|Szolgáltatás  |Leírás  |
|---------|---------|
|Anomáliadetektálás valós időben. | A streamelési adatok rendellenességének észlelése a korábban látott adatpontok használatával annak megállapításához, hogy a legutóbbi anomália-e. Ez a művelet létrehoz egy modellt a küldött adatpontok használatával, és megállapítja, hogy a célpont anomália-e. Ha minden létrehozott új adatponttal hívja meg az API-t, a létrehozott adatokat monitorozással figyelheti. |
|Anomáliákat észlel az adatkészletben kötegként. | Az idősorozat segítségével észlelheti az adatokban esetlegesen meglévő rendellenességeket. Ez a művelet létrehoz egy modellt a teljes idősorozat-adatok alapján, és minden pontot ugyanaz a modell elemez.         |
|Észlelheti a változási pontokat az adatkészletben kötegként. | Az idősorozat segítségével észlelheti az adatokban található trendváltozási pontokat. Ez a művelet létrehoz egy modellt a teljes idősorozat-adatok alapján, és minden pontot ugyanaz a modell elemez.    |
| További információk az adatokról. | Hasznos részletek az adatokról és a megfigyelt anomáliákról, beleértve a várt értékeket, anomáliahatárokat és pozíciókat. |
| Az anomáliadetektálási határok beállítása. | A anomáliadetektor API automatikusan határokat hoz létre az anomáliadetektáláshoz. Állítsa be ezeket a határokat, hogy növelje vagy csökkentse az API adatanomáliákkal szembeni érzékenységét, és jobban illeszkedjen az adatokhoz. |

## <a name="demo"></a>Bemutató

Tekintse meg ezt [az interaktív bemutatót,](https://aka.ms/adDemo) amelyből anomáliadetektor működését.
A bemutató futtatásához létre kell hoznia egy anomáliadetektor, és be kell szereznie az API-kulcsot és a végpontot.

## <a name="notebook"></a>Jegyzetfüzet

Ha meg szeretne ismerkedni a anomáliadetektor API hívásának mikéntjére, próbálja ki ezt a [jegyzetfüzetet.](https://aka.ms/adNotebook) Ez Jupyter Notebook bemutatja, hogyan küldhet API-kérést, és hogyan vizualizálhatja az eredményt.

A jegyzetfüzet futtatásához kövesse az alábbi lépéseket:

1. Szerezze be az anomáliadetektor API előfizetői kulcsát és egy API-végpontot. Az alábbi szakasz a regisztrációra vonatkozó utasításokat tartalmazza.
1. Jelentkezzen be, és válassza a Klónozás lehetőséget a jobb felső sarokban.
1. A klónozási művelet befejezése előtt törölje a "nyilvános" beállítás jelölését a párbeszédpanelen, ellenkező esetben a jegyzetfüzet nyilvános lesz, beleértve az előfizetői kulcsokat is.
1. Válassza **a Futtatás ingyenes számításon lehetőséget**
1. Válassza ki az egyik jegyzetfüzetet.
1. Adja hozzá az anomáliadetektor API előfizetői kulcsát a `subscription_key` változóhoz.
1. Módosítsa a `endpoint` változót a végpontra. Például: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. A felső menüsávon válassza a **Cella,** majd **az Összes futtatása lehetőséget.**

## <a name="workflow"></a>Munkafolyamat

A anomáliadetektor API egy RESTful-webszolgáltatás, így egyszerűen hívható bármilyen programozási nyelvről, amely képes HTTP-kérések igénylésére és JSON-adatokat elemezni.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

A regisztráció után:

1. Az idősorozat-adatokat konvertálja érvényes JSON formátumba. Az [adatok előkészítésekor](concepts/anomaly-detection-best-practices.md) ajánlott eljárásokat kell használnia a legjobb eredmények elérése érdekében.
1. Küldjön egy kérést a anomáliadetektor API-nak az adataival.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="algorithms"></a>Algoritmusok

* A használt algoritmusokkal kapcsolatos információkért tekintse meg a következő technikai blogokat:
    * [Az Azure Anomaly Detector API bemutatása](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Az Azure-beli SR-CNN algoritmus áttekintése anomáliadetektor](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Olvassa el a [Microsoft Time-Series Anomaly Detection Service](https://arxiv.org/abs/1906.03821) (A KDD 2019 által elfogadott) tanulmányát, amely a Microsoft által fejlesztett SR-CNN algoritmusokat mutatja be.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>Szolgáltatás rendelkezésre állása és redundancia

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Rugalmas a anomáliadetektor szolgáltatászóna?

Igen. A anomáliadetektor szolgáltatás alapértelmezés szerint rugalmas a zónák számára.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>Hogyan a anomáliadetektor szolgáltatást zónaredőképesre konfigurálni?

A zóna rugalmasságának engedélyezéséhez nincs szükség ügyfél-konfigurációra. Az erőforrások zónatűrése anomáliadetektor alapértelmezés szerint elérhető, és maga a szolgáltatás kezeli.

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

[A anomáliadetektor api-funkciók](anomaly-detector-container-howto.md) helyszíni üzembe helyezéséhez használjon anomáliadetektor tárolókat. A Docker-tárolók lehetővé teszik, hogy megfelelőségi, biztonsági vagy egyéb működési okokból közelebb hozza a szolgáltatást az adataihoz.

## <a name="join-the-anomaly-detector-community"></a>Csatlakozás az Anomáliadetektor-közösséghez

* Csatlakozzon [a anomáliadetektor Advisors csoporthoz a Microsoft Teamsben](https://aka.ms/AdAdvisorsJoin)
* A kiválasztott [felhasználó által létrehozott tartalom](user-generated-content.md)

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Idősorozat-adatok rendellenességének észlelése az anomáliadetektor](quickstarts/client-libraries.md)
* A anomáliadetektor API [online bemutatója](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook)
* A anomáliadetektor [REST API hivatkozás](https://aka.ms/anomaly-detector-rest-api-ref)
