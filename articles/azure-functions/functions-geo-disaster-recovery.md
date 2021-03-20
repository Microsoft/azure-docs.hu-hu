---
title: Azure Functions geo-vész-helyreállítás és magas rendelkezésre állás
description: A földrajzi régiók használata a redundancia és a feladatátvétel során a Azure Functionsban.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506281"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geo-vész-helyreállítás

Ha a teljes Azure-régiók vagy-adatközpontok leállást tapasztalnak, kritikus fontosságú a számítási feladatok egy másik régióban való feldolgozásának folytatásához.  Ebből a cikkből megtudhatja, milyen stratégiákat alkalmazhat a funkciók üzembe helyezéséhez a vész-helyreállítás engedélyezéséhez.

## <a name="basic-concepts"></a>Alapfogalmak

A Azure Functions egy adott régióban futnak.  A magasabb rendelkezésre állás érdekében több régióban is telepítheti ugyanezeket a funkciókat.  Több régióban a függvények *aktív/aktív* mintában vagy *aktív/passzív* mintában is futhatnak.  

* Aktív/aktív. Mindkét régió aktív és eseményeket fogad (ismétlődő vagy rotációs). Az aktív/aktív funkció a HTTPS-függvények esetében ajánlott az Azure bejárati ajtóval együtt.
* Aktív/passzív. Az egyik régió aktív és fogad eseményeket, míg a másodlagos üresjáratban van.  Ha feladatátvételre van szükség, a másodlagos régió aktiválva lesz, és átveszi a feldolgozást.  Ez olyan nem HTTP-függvények esetén ajánlott, mint a Service Bus és a Event Hubs.

További információ a többrégiós környezetekről: [alkalmazások futtatása több régióban](/azure/architecture/reference-architectures/app-service-web-app/multi-region) .

## <a name="activeactive-for-https-functions"></a>Aktív/aktív HTTPS-függvények esetén

A függvények aktív/aktív üzembe helyezéséhez olyan összetevőre van szükség, amely képes a két régió közötti események koordinálására.  A HTTPS-függvények esetében ez a koordináció az [Azure bejárati ajtaján](../frontdoor/front-door-overview.md)keresztül valósítható meg.  Az Azure bejárati ajtói több regionális függvény között is átirányíthatók és ciklikusan lekerekített HTTPS-kérelmek.  Emellett rendszeres időközönként ellenőrzi az egyes végpontok állapotát.  Ha egy regionális függvény nem válaszol az állapot-ellenőrzésekre, az Azure bejárati ajtaja kikerül a forgásból, és csak az egészséges funkciók felé továbbítja a forgalmat.  

![Architektúra az Azure bejárati ajtó és funkció számára](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktív/aktív a nem HTTPS függvények esetében

A nem HTTPS függvények esetében továbbra is aktív/aktív üzemelő példányok érhetők el.  Azonban érdemes megfontolnia, hogy a két régió hogyan kommunikáljon egymással, illetve hogyan koordinálja őket egymással.  Ha ugyanezt a függvényt két régióba telepítette, az egyes aktiválások ugyanarra a Service Bus várólistára kerülnek, akkor azok versengő fogyasztóként működhetnek a várólistán.  Ez azt jelenti, hogy az egyes üzeneteket csak az egyik példány dolgozza fel, ez azt is jelenti, hogy az adott Service Buson továbbra is egyetlen meghibásodási pont van.  Ha két Service Bus várólistát helyez üzembe (egyet egy elsődleges régióban, egy másodlagos régióban), és a két Function-alkalmazás a régiók várólistájára mutat, a kérdés most a két régió között a várólista-üzenetek terjesztésének módját mutatja be.  Ez gyakran azt jelenti, hogy minden közzétevő megpróbál *mindkét* régióba közzétenni egy üzenetet, és az egyes üzeneteket az aktív Function apps is dolgozza fel.  Míg ez aktív/aktív mintát hoz létre, a számítási feladatok ismétlődését, valamint az adatok összesítésének időpontját és módját is létrehozhatja.  Ezen okok miatt ajánlott a nem HTTPS-eseményindítók használata az aktív/passzív minta használatára.

## <a name="activepassive-for-non-https-functions"></a>Aktív/passzív a nem HTTPS függvények esetében

Az aktív/passzív mód lehetővé teszi, hogy csak egyetlen függvény dolgozza fel az egyes üzeneteket, azonban egy olyan mechanizmust biztosít, amely egy másodlagos régióba kerül át, vészhelyzet esetén.  A Azure Functions [Azure Service Bus geo-helyreállítási](../service-bus-messaging/service-bus-geo-dr.md) és [Azure Event Hubs geo-helyreállítási funkció](../event-hubs/event-hubs-geo-dr.md)mellett működik.

Az Azure Event Hubs Triggerek használata például az aktív/passzív minta a következőket vonja maga után:

* Az Azure Event hub egy elsődleges és egy másodlagos régióban is üzembe lett helyezve.
* Földrajzi katasztrófa engedélyezve az elsődleges és a másodlagos Event hub párosításához.  Ez létrehoz egy "aliast" is, amellyel csatlakozhat az Event hubokhoz, és átválthat az elsődlegesről a másodlagosra a kapcsolati adatok módosítása nélkül.
* Az elsődleges és a másodlagos régióra telepített alkalmazások.
* A Function apps a *közvetlen* (nem alias) kapcsolati sztringet indítja el a megfelelő Event hub esetében. 
* Az Event hub közzétevői számára közzé kell tenni az alias kapcsolati karakterláncát. 

![Aktív – passzív példa architektúra](media/functions-geo-dr/active-passive.png)

A feladatátvétel előtt a megosztott aliasnak küldött közzétevők átirányítják az elsődleges esemény központját.  Az elsődleges Function alkalmazás kizárólag az elsődleges Event hub-t figyeli.  A másodlagos Function alkalmazás passzív és tétlen lesz.  Amint a feladatátvételt kezdeményezik, a megosztott aliasra küldött közzétevők most a másodlagos esemény központját fogják irányítani.  A másodlagos Function alkalmazás mostantól aktív lesz, és automatikusan elindítja az indítást.  A másodlagos régióra történő hatékony feladatátvétel kizárólag az Event hub-ból hajtható végre, és csak akkor válik aktívvá a függvények, ha a megfelelő Event hub aktív.

A feladatátvételsel kapcsolatos tudnivalókat és szempontokat a [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) és az [Event hubok](../event-hubs/event-hubs-geo-dr.md)című témakörben olvashatja.

## <a name="next-steps"></a>Következő lépések

* [Azure-beli bejárati ajtó létrehozása](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs feladatátvételi megfontolások](../event-hubs/event-hubs-geo-dr.md#considerations)
