---
title: Tervezze meg Gen2-környezetét – Azure Time Series Insights | Microsoft Docs
description: Ajánlott eljárások a Azure Time Series Insights Gen2-környezet konfigurálásához, kezeléséhez, megtervezéséhez és üzembe helyezéséhez.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016785"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>A Azure Time Series Insights Gen2-környezet megtervezése

Ez a cikk az Azure Time Series Insights Gen2 használatával történő gyors megtervezéséhez és az első lépések elkezdéséhez ajánlott eljárásokat ismerteti.

## <a name="best-practices-for-planning-and-preparation"></a>Ajánlott eljárások a tervezéshez és előkészítéshez

A környezet tervezésével és előkészítésével kapcsolatos ajánlott eljárásokat a következő cikkekben találja:

* [Azure Time Series Insights Gen2-környezet kiépítésekor](#the-gen2-environment)kapott tudnivalók.
* Az [idősorozat-azonosítók és az időbélyeg tulajdonságai](#configure-time-series-ids-and-timestamp-properties).
* Az új [Idősorozat-modell](#understand-the-time-series-model), valamint a saját létrehozása.
* [Események hatékony küldése a JSON-ban](#shape-your-events).
* Azure Time Series Insights [üzleti katasztrófák helyreállítási lehetőségei](#business-disaster-recovery).

A Azure Time Series Insights az üzleti modellt is alkalmazza. További információ a díjakról és a kapacitásról: [Azure Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>A Gen2-környezet

Azure Time Series Insights Gen2-környezet kiépítésekor két Azure-erőforrást hoz létre:

* Azure Time Series Insights Gen2-környezet
* Azure Storage-fiók

A kiépítési folyamat részeként meg kell adnia, hogy szeretné-e engedélyezni a meleg tárolót. A meleg tárolás többplatformos lekérdezési élményt biztosít. Ha engedélyezve van, meg kell adnia egy 7 és 30 nap közötti megőrzési időtartamot. A meleg tárolási megőrzési időszakon belül végrehajtott lekérdezések általában gyorsabb válaszidőt biztosítanak. Ha egy lekérdezés a meleg tároló megőrzési időtartamára terjed ki, a rendszer a hűtőházi tárolóból kézbesíti.

A melegen tárolt lekérdezések ingyenesek, míg a hűtőházi tárolással kapcsolatos lekérdezések költségekkel járnak. Fontos megérteni a lekérdezési mintákat, és ennek megfelelően tervezze meg a meleg tároló konfigurációját. Azt javasoljuk, hogy az interaktív elemzések a legújabb, a meleg áruházban található és a minták elemzése, valamint a hosszú távú trendek esetében is naprakészek legyenek.

> [!NOTE]
> Ha többet szeretne megtudni a meleg adatainak lekérdezéséről, olvassa el az [API-referenciát](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters).

A kezdéshez három további elemre van szükség:

* Egy [Idősorozat-modell](./concepts-model-overview.md)
* [Time Series Insightshoz csatlakoztatott eseményforrás](./concepts-streaming-ingestion-event-sources.md)
* [Az eseményforrás](./time-series-insights-send-events.md) , amely a modellhez van rendelve, és érvényes JSON formátumú.

## <a name="review-azure-time-series-insights-gen2-limits"></a>Azure Time Series Insights Gen2 korlátainak áttekintése

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Idősorozat-azonosítók és időbélyeg-tulajdonságok konfigurálása

Új Azure Time Series Insights-környezet létrehozásához válasszon egy idősorozat-azonosítót. Ez logikai partícióként működik az adatai számára. Ahogy azt említettük, győződjön meg arról, hogy az idősorozat-azonosítók készen állnak.

> [!IMPORTANT]
> Az idősorozat-azonosítókat *később nem lehet módosítani*. Győződjön meg róla, hogy a végső kiválasztás és az első használat előtt ellenőrizzen egyet.

Az erőforrások egyedi megkülönböztetéséhez legfeljebb három kulcsot választhat ki. További információ: [ajánlott eljárások az idősorozat-azonosító és a betöltési szabályok kiválasztásához](./how-to-select-tsid.md) . [](concepts-json-flattening-escaping-rules.md)

Az **időbélyeg** tulajdonság szintén fontos. Ezt a tulajdonságot az eseményforrás hozzáadásakor lehet kijelölni. Minden eseményforrás egy nem kötelező időbélyeg-tulajdonsággal rendelkezik, amely az események időbeli alakulásának nyomon követésére szolgál. Az időbélyegző értékei megkülönböztetik a kis-és nagybetűket, és az egyes eseményforrás egyedi leírását kell formázni.

Ha üresen hagyja, a rendszer az eseményt várólistán lévő az IoT Hub vagy az Event hub esemény időbélyegzőként való használatakor. Általánosságban elmondható, hogy a felhasználóknak meg kell választaniuk az időbélyegző tulajdonságot, és azt az időpontot kell használniuk, amikor az érzékelő vagy címke a hub várólistán lévő idejének helyett az olvasást generálta. További információ és az időzóna-eltolások olvasása az [eseményforrás időbélyegzője](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)alapján.

## <a name="understand-the-time-series-model"></a>Az idősorozat-modell ismertetése

Mostantól konfigurálhatja Azure Time Series Insights környezete idősorozat-modelljét. Az új modell segítségével könnyedén megtalálhatja és elemezheti a IoT-adatbázisokat. Lehetővé teszi a Time Series-adatgyűjtési és-bővítési feladatok elvégzését, és segít a felhasználó által készített adatkészletek előkészítésében. A modell idősorozat-azonosítókat használ, amelyek olyan példányhoz képezhetők le, amely társítja az egyedi erőforrást változókkal, más néven típusokkal és hierarchiákkal. További információért olvassa el az [idősorozat-modell](./concepts-model-overview.md) áttekintését.

A modell dinamikus, így bármikor létrehozható. A gyors kezdéshez hozza létre és töltse fel az adatoknak a Azure Time Series Insightsba való kitolása előtt. A modell létrehozásához olvassa el [az idősorozat-modell használatát](./concepts-model-overview.md)ismertetőt.

Sok ügyfél esetében az idősorozat-modell egy már meglévő eszköz-modellel vagy ERP-rendszerrel van leképezve. Ha nem rendelkezik meglévő modellel, a rendszer előre elkészített felhasználói élményt [biztosít](https://github.com/Microsoft/tsiclient) a gyors üzembe helyezéshez. Ha szeretné elképzelni, hogyan segíthet a modell, tekintse meg a [minta bemutató környezetét](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Az események formázása

Ellenőrizheti, hogy az események hogyan küldhetők Azure Time Series Insightsba. Ideális esetben a rendszer jól és hatékonyan denormalizálja az eseményeket.

Egy jó ökölszabály:

* Az idősorozat-modellben tárolja a metaadatokat.
* Győződjön meg arról, hogy az idősoros mód, a példány mezői és az események csak a szükséges információkat tartalmazzák, például egy idősorozat-azonosítót vagy egy időbélyeg-tulajdonságot.

További információért és az események összeolvasztásának és tárolásának megismeréséhez olvassa el a [JSON-összeolvasztási és-Escape-szabályokat](./concepts-json-flattening-escaping-rules.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Következő lépések

* Tekintse át [Azure Advisor](../advisor/advisor-overview.md) az üzleti helyreállítás konfigurációs beállításainak megtervezéséhez.
* Tekintse át [Azure Advisor](../advisor/advisor-overview.md) az üzleti helyreállítás konfigurációs beállításainak megtervezéséhez.
* További információ az [adatfeldolgozásról](./concepts-ingestion-overview.md) Azure Time Series Insights Gen2.
* Tekintse át az [adattárolásról](./concepts-storage.md) szóló cikket Azure Time Series Insights Gen2.
* Tudnivalók az [adatmodellezésről](./concepts-model-overview.md) Azure Time Series Insights Gen2.