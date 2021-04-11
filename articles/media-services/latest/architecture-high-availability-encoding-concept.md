---
title: Magas rendelkezésre állás Media Services videó igény szerint
description: Ez a cikk áttekintést nyújt a VOD-alkalmazások magas rendelkezésre állásának megkönnyítésére használható Azure-szolgáltatásokról.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c334888f5b85b0d2211225282680d5f791b50793
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277878"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Magas rendelkezésre állás a Media Services és a videó igény szerint (VOD)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>Magas rendelkezésre állás a VOD-hoz

A magas rendelkezésre állású, [Geodes](/azure/architecture/patterns/geodes) nevű tervezési minta az Azure Architecture dokumentációjában található. Ismerteti, hogyan történik az ismétlődő erőforrások üzembe helyezése különböző földrajzi régiókban a méretezhetőség és a rugalmasság biztosítása érdekében.  Az Azure-szolgáltatások használatával olyan architektúrát hozhat létre, amely számos magas rendelkezésre állású tervezési szempontot, például redundanciát, állapot-figyelést, terheléselosztást és adatbiztonsági mentést és helyreállítást biztosít.  Az alábbi architektúrát a megoldásban használt egyes szolgáltatásokra vonatkozó részletek ismertetik, valamint azt, hogy az egyes szolgáltatások hogyan használhatók magas rendelkezésre állású architektúra létrehozásához a VOD-alkalmazásokhoz.

### <a name="sample"></a>Sample

Rendelkezésre áll egy példa arra, hogy a magas rendelkezésre állást a Media Services és a video on demand (VOD) használatával Ismerkedjen meg. Emellett részletesebben is megtudhatja, hogyan használják a szolgáltatásokat a VOD-forgatókönyvekhez.  A minta nem használható éles környezetben a jelenlegi formájában.  Körültekintően tekintse át a mintakód és a readme című szakaszt, különösen a [meghibásodási módok](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) szakaszát, mielőtt integrálja azt egy éles alkalmazásba.  A magas rendelkezésre állású video on demand (VOD) termelési megvalósításának alapos áttekintést kell adnia a Content Delivery Network (CDN) stratégiáról.  Tekintse [meg a kódot a githubon](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## <a name="overview-of-services"></a>A szolgáltatások áttekintése

A példában használt szolgáltatások a következők:

| Ikon | Név | Leírás |
| :--: | ---- | ----------- |
|![Ez a Media Services-fiók ikonja.](./media/architecture-high-availability-encoding-concept/azure-media-services.svg)| Media Services-fiók | **Leírás:**<br>A Media Services fiók az Azure-ban a médiatartalom kezelésére, titkosítására, kódolására, elemzésére és továbbítására szolgáló kiindulópont. Egy Azure Storage-fiók erőforráshoz van társítva. A fióknak és az összes társított tárterületnek ugyanahhoz az Azure-előfizetéshez kell tartoznia.<br><br>**VOD használata:**<br>Ezek a szolgáltatások a videó-és hangeszközök kódolására és továbbítására használhatók.  A magas rendelkezésre állás érdekében legalább két Media Services fiókot kell beállítania, amelyek mindegyike egy másik régióban található. [További információ a Azure Media Servicesról](media-services-overview.md). |
|![Ez a Storage-fiók ikonja.](./media/architecture-high-availability-encoding-concept/storage-account.svg)| Tárfiók | **Leírás:**<br>Egy Azure Storage-fiók tartalmazza az összes Azure Storage-adatobjektumot: Blobok, fájlok, várólisták, táblák és lemezek. Az adatok a világon bárhonnan elérhetők HTTP-n vagy HTTPS-en keresztül.<br><br>Az egyes régiókban minden Media Services fióknak ugyanabban a régióban kell lennie.<br><br>**VOD használata:**<br>A bemeneti és kimeneti adatokat a VOD-feldolgozáshoz és a folyamatos átvitelhez is tárolhatja. [További információ az Azure Storage-ról](../../storage/common/storage-introduction.md). |
|![Ez az Azure Storage üzenetsor ikonja.](./media/architecture-high-availability-encoding-concept/storage-account-queue.svg)| Azure Storage Queue | **Leírás:**<br>Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők.<br><br>**VOD használata:**<br>A várólisták segítségével üzeneteket küldhet és fogadhat, hogy a különböző modulok között összehangolja a tevékenységeket. A minta egy Azure Storage-várólistát használ, de az Azure más típusú várólistákat is biztosít, például a Service Bus és Service Fabric megbízható várólistákat, amelyek jobban illeszkednek az igényeihez. [További információ az Azure üzenetsorről](../../storage/queues/storage-queues-introduction.md). |
|![Ez a Azure Cosmos DB ikon.](./media/architecture-high-availability-encoding-concept/azure-cosmos-db.svg)| Azure Cosmos DB  | **Leírás:**<br>A Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása, amely egymástól függetlenül méretezi az átviteli sebességet és a tárterületet a globálisan tetszőleges számú Azure-régióban.<br><br>**VOD használata:**<br>A táblák a feladatok kimeneti állapotára vonatkozó rekordok tárolására és az egyes Media Services-példányok állapotának nyomon követésére használhatók. Az Media Services API-nak az egyes hívások állapotát is nyomon követheti/rögzítheti. [További információ a Azure Cosmos DBról](../../cosmos-db/introduction.md).  |
|![Ez a felügyelt identitás ikonja.](./media/architecture-high-availability-encoding-concept/managed-identity.svg)| Felügyelt identitás | **Leírás:**<br>A felügyelt identitás az Azure AD egyik funkciója, amely automatikusan felügyelt identitást biztosít az Azure AD-ben. A hitelesítés bármely olyan szolgáltatáshoz használható, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vaultt is, a hitelesítő adatok kódban való tárolása nélkül.<br><br>**VOD használata:**<br>Azure Functions a felügyelt identitás használatával hitelesítheti Media Services példányokat a Key Vaulthoz való kapcsolódáshoz. [További információ a felügyelt identitásról](../../active-directory/managed-identities-azure-resources/overview.md). |
|![Ez a Key Vault ikon.](./media/architecture-high-availability-encoding-concept/key-vault.svg)| Key Vault | **Leírás:**<br>A Azure Key Vault a tokenekhez, jelszavakhoz, tanúsítványokhoz, API-kulcsokhoz és egyéb titkokhoz való hozzáférés biztonságos tárolásához és szigorú szabályozásához használható. Emellett kulcsfontosságú felügyeleti megoldásként is használható. Az Azure Key Vaulttal egyszerűen létrehozhatja és vezérelheti az adatok titkosításához használt titkosítási kulcsokat. Az Azure-hoz és a belső csatlakoztatott erőforrásokhoz használható nyilvános és magánhálózati Transport Layer Security/SSL (TLS/SSL) tanúsítványokat egyszerűen üzembe helyezheti, kezelheti és telepítheti. A titkokat és a kulcsokat a szoftverrel vagy az FIPS 140-2 2-es szintű hitelesített HSM lehet védeni.<br><br>**VOD használata:**<br>Key Vault az alkalmazáshoz tartozó szolgáltatásnév hozzáférési házirendjeinek beállítására használható.  A kapcsolódási karakterlánc tárolása a Storage-fiókok számára lehetséges. A kapcsolódási karakterláncokat a Storage-fiókokhoz és a Cosmos db-hez Key Vault használjuk. A fürt általános konfigurációjának tárolására Key Vault is használhatja. Az egyes Media Service-példányok esetében az előfizetés-azonosító, az erőforráscsoport neve és a fiók neve is tárolható. További részletekért lásd: hogyan használják a mintában. [További információ a Key Vaultról](../../key-vault/general/overview.md). |
|![Ez a Azure Functions ikon.](./media/architecture-high-availability-encoding-concept/function-app.svg)| Azure Functions | **Leírás:**<br>Futtasson kis mennyiségű kódot ("functions" néven) anélkül, hogy az alkalmazás-infrastruktúrával kellene foglalkoznia Azure Functionsokkal. [További információ a Azure Functionsról](../../azure-functions/functions-overview.md).<br><br>**VOD használata:**<br>A Azure Functions használható a VOD-alkalmazás moduljainak tárolására.  A VOD-alkalmazások moduljai a következők lehetnek:<br><br>**Feladatütemezés modul**<br>A feladatütemezés modul új feladatok Media Services fürtbe való beküldésére használható (két vagy több példány különböző régiókban). Nyomon követheti az egyes Media Services példányok állapotát, és új feladatot küldhet a következő Kifogástalan állapotra.<br><br>**Feladatok állapotának modulja**<br>A feladatok állapota modul a Azure Event Grid szolgáltatástól érkező feladatok kimeneti állapotával kapcsolatos eseményeket figyeli. Az eseményeket az Event Store-ba fogja tárolni, hogy csökkentse a modulok többi részén Media Services API-k számát.<br><br>**Példány állapota modul**<br>Ez a modul nyomon követheti az elküldött feladatokat, és meghatározhatja az egyes Media Services-példányok állapotát. Nyomon követheti a befejezett feladatokat, a sikertelen feladatokat és a nem befejezett feladatokat.<br><br>**Üzembe helyezési modul**<br>Ez a modul felépíti a feldolgozott eszközöket. Az adategységeket az összes Media Services példányba másolja, és beállítja az Azure bejárati szolgáltatását annak biztosítására, hogy az eszközök akkor is továbbíthatók legyenek, ha egyes Media Services példányok nem voltak elérhetők. Emellett a streaming-lokátorokat is beállítja.<br><br>**Feladatot ellenőrző modul**<br>Ez a modul nyomon követheti az összes elküldött feladatot, újraküldheti a sikertelen feladatokat, és elvégezheti a feladat-visszaírást, ha a feladat sikeresen befejeződött.  |
|![Ez a App Service ikon.](./media/architecture-high-availability-encoding-concept/application-service.svg)| App Service (és terv)  | **Leírás:**<br>A Azure App Service egy HTTP-alapú szolgáltatás, amellyel webalkalmazásokat, REST API-kat és mobil back-végpontokat üzemeltet. Támogatja a .NET, a .NET Core, a Java, a Ruby, a Node.js, a PHP vagy a Python platformot. Az alkalmazások Windows-és Linux-alapú környezetekben futnak és méretezhetők.<br><br>**VOD használata:**<br>Az egyes modulokat egy App Service üzemelteti. [További információ a app Serviceról](../../app-service/overview.md). |
|![Ez az Azure bejárati ajtó ikonja.](./media/architecture-high-availability-encoding-concept/azure-front-door.svg)| Azure Front Door | **Leírás:**<br>Az Azure-beli bejárati ajtó a webes forgalom globális útválasztásának meghatározására, kezelésére és figyelésére használható a legjobb teljesítmény és a gyors globális feladatátvétel érdekében a magas rendelkezésre állás érdekében.<br><br>**VOD használata:**<br>Az Azure-beli bejárati ajtó használatával átirányíthatja a forgalmat a streaming-végpontokra. [További információ az Azure bejáratáról](../../frontdoor/front-door-overview.md).  |
|![Ez a Azure Event Grid ikon.](./media/architecture-high-availability-encoding-concept/event-grid-subscription.svg)| Azure Event Grid | **Leírás:**<br>Az eseményvezérelt architektúrák számára készült Event Grid beépített támogatást nyújt az Azure-szolgáltatásokból érkező eseményekhez, például a Storage-blobokhoz és az erőforráscsoportokhoz. Emellett támogatja az egyéni témakör eseményeit is. A szűrők segítségével adott eseményeket átirányíthat különböző végpontokra, csoportos küldést végezhet több végpontra, és biztosíthatja az események megbízható kézbesítését. Maximalizálja a rendelkezésre állást azáltal, hogy az összes régióban, illetve a rendelkezésre állási zónák között natív módon terjeszti a több tartalék tartományt.<br><br>**VOD használata:**<br>Event Grid segítségével nyomon követheti az összes alkalmazási eseményt, és tárolhatja őket a feladatok állapotának megőrzése érdekében. [További információ a Azure Event Gridról](../../event-grid/overview.md). |
|![Ez a Application Insights ikon.](./media/architecture-high-availability-encoding-concept/application-insights.svg)| Application Insights | **Leírás:** <br>Az Application Insights az Azure Monitor egy olyan funkciója, amely egy bővíthető alkalmazásteljesítmény-kezelési (Application Performance Management, APM) szolgáltatás fejlesztők és DevOps-szakemberek számára. Az élő alkalmazások figyelésére szolgál. Észleli a teljesítménnyel kapcsolatos rendellenességeket, és elemzési eszközöket tartalmaz a problémák diagnosztizálásához és a felhasználók alkalmazásokkal való ellátásának megismeréséhez. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.<br><br>**VOD használata:**<br>A rendszer minden naplót elküldhet a Application Insightsba. Előfordulhat, hogy a sikeresen létrehozott feladatokra való rákereséssel megtekintheti, hogy a rendszer milyen példányt dolgoz fel az egyes feladatokhoz. Az összes elküldött feladatot tartalmazhatja, beleértve az egyedi azonosítót és a példánynév adatait is. [További információ a Application Insightsról](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Architektúra

Ez a magas szintű diagram a rendelkezésre álló minta architektúráját mutatja be a magas rendelkezésre állással és a Media Services szolgáltatással való ismerkedéshez.

[![Igény szerinti videó (VoD) – magas szintű architektúra diagramja ](media/architecture-high-availability-encoding-concept/high-availability-architecture.svg)](./media/architecture-high-availability-encoding-concept/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="regions"></a>Régiók

* [Hozzon létre](./account-create-how-to.md) két (vagy több) Azure Media Services fiókot. A két fióknak különböző régiókban kell lennie. További információ: [a Azure Media Services szolgáltatást telepítő régiók](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Töltse fel az adathordozót ugyanabba a régióba, ahonnan el szeretné küldeni a feladatot. A kódolás megkezdésével kapcsolatos további információkért lásd: [a feladatok bevitele HTTPS URL-](./job-input-from-http-how-to.md) címről vagy egy [helyi fájlból származó feladatok létrehozása](./job-input-from-local-file-how-to.md).
* Ha ezt követően újra el kell küldenie a [feladatot](./transform-jobs-concept.md) egy másik régióba, használhatja `JobInputHttp` vagy használhatja az `Copy-Blob` adatok másolását a forrásként szolgáló tárolóból a másik régióban lévő eszköz-tárolóba.

### <a name="monitoring"></a>Figyelés

* Fizessen elő az `JobStateChange` egyes fiókokban lévő üzenetekre Azure Event Gridon keresztül.
    * Az [események regisztrálása](./reacting-to-media-services-events.md) a Azure Portal vagy a CLI használatával (ezt a Event Grid Management SDK-val is elvégezheti)
    * Használja a [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) -t (amely natív módon támogatja az Media Services eseményeket).
    * Azure Functions használatával Event Grid eseményeket is felhasználhat.

    További információk:

    * Tekintse meg a [Hangelemzési mintát](./transform-jobs-concept.md) , amely bemutatja, hogyan figyelheti meg a feladatokat a Azure Event Grid, beleértve a tartalék hozzáadását abban az esetben, ha a Azure Event Grid üzenetek valamilyen okból késleltetve vannak.
    * Tekintse meg [Media Services események Azure Event Grid sémáit](./media-services-event-schemas.md).

* A [feladatok](./transform-jobs-concept.md)létrehozásakor:
    * Véletlenszerűen válasszon ki egy fiókot a jelenleg használt fiókok listájából (ez a lista általában mindkét fiókot tartalmazza, de ha problémát észlel, akkor csak egy fiókot tartalmazhat). Ha a lista üres, riasztást küld, hogy az operátor megvizsgálja.
    * Hozzon létre egy rekordot, amellyel nyomon követheti az egyes fedélzeti feladatokat, valamint a használt régiót/fiókot.
* Ha a `JobStateChange` kezelő értesítést kap arról, hogy egy adott tevékenység elérte az ütemezett állapotot, jegyezze fel az ütemezett állapotba és a használt régióba/fiókba való belépés időpontját.
* Ha a `JobStateChange` kezelő értesítést kap arról, hogy egy adott feladatot elérte a feldolgozási állapotot, a feladathoz tartozó rekordot megjelölve feldolgozza a feldolgozást, és rögzíti a feldolgozási állapotba való belépés időpontját.
* Ha a `JobStateChange` kezelő értesítést kap arról, hogy a feladat elérte a végső állapotot (befejezett/hibás/megszakított), akkor a megfelelő rekordot kell megjelölnie a feladathoz.
* Külön folyamattal kell rendelkeznie, amely rendszeresen megvizsgálja a feladatok rekordjait
    * Ha az ütemezett állapot olyan feladatokkal rendelkezik, amelyek egy adott régióra vonatkozóan ésszerű időn belül nem fejlettek a feldolgozási állapotra, távolítsa el a régiót a jelenleg használt fiókok listájáról. Az üzleti igényektől függően dönthet úgy, hogy azonnal megszakítja a feladatokat, és visszaküldi azokat a másik régióba. Vagy további időt adhat nekik, hogy a következő állapotba lépjenek.
    * Ha egy régiót eltávolítottak a fiók listájából, figyelje a helyreállítást a listához való hozzáadás előtt. A regionális állapot a régió meglévő feladatain keresztül figyelhető (ha nem lettek megszakítva és nem lettek elküldve), a fiók egy adott időtartam után visszakerül a listához, és az operátorok figyelik az Azure-kommunikációt az olyan kimaradások miatt, amelyek hatással lehetnek a Azure Media Servicesra.

## <a name="next-steps"></a>Következő lépések

* Példák a [kód](/samples/browse/?products=azure-media-services) megadására