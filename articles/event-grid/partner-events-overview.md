---
title: Azure Event Grid – partneri események
description: A harmadik féltől származó Event Grid SaaS-és Pásti-partnerektől származó eseményeket közvetlenül az Azure-szolgáltatásoknak küldheti el Azure Event Grid.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94506146"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Partneri események a Azure Event Gridban (előzetes verzió)
A **partner Events** szolgáltatás lehetővé teszi, hogy a harmadik féltől származó SaaS-szolgáltató eseményeket tegyen közzé a szolgáltatásaiból, így a felhasználók előfizethetnek ezekre az eseményekre. Ez a funkció egy, a [témakör](concepts.md#topics) típusával, egy **partnerrel kapcsolatos témakörben** elérhetővé teszi a harmadik féltől származó eseményekhez való külső élményt. Az előfizetők előfizetéseket hoznak létre ennek a témakörnek az események felhasználásához. Emellett egy tiszta pub-sub modellt is biztosít az esemény-közzétevők és az előfizetők által használt erőforrások elkülönítésével és az azok tulajdonjogával kapcsolatban.

> [!NOTE]
> Ha még nem ismeri a Event Grid használatát, tekintse meg az [áttekintést](overview.md), a [fogalmakat](concepts.md)és az [eseménykezelőket](event-handlers.md)ismertető cikket.

## <a name="what-is-partner-events-to-a-publisher"></a>Mi a közzétevőhöz kapcsolódó partneri esemény?
Egy esemény-közzétevőhöz a partner Events szolgáltatás lehetővé teszi a közzétevők számára a következő feladatok elvégzését:

- Az eseményforrás bevezetése Event Grid
- Hozzon létre egy névteret (végpontot), amely számára az eseményeket közzé lehet tenni
- Hozzon létre partneri témákat az Azure-ban, hogy az előfizetők saját és használati felhasználják az eseményeket

## <a name="what-is-partner-events-to-a-subscriber"></a>Mi az előfizető partneri eseményei?
Egy előfizető számára a partner Events szolgáltatás lehetővé teszi, hogy az Azure-ban partneri témákat hozzon létre, hogy a harmadik féltől származó eseményekről származó eseményeket használjanak. Az esemény-felhasználást olyan esemény-előfizetések létrehozásával lehet megvalósítani, amelyek az előfizető eseménykezelője számára küldenek (leküldéses) eseményeket.

## <a name="why-should-i-use-partner-events"></a>Miért érdemes a partneri eseményeket használni?
Ha az alábbi követelmények közül egyet vagy többet szeretne használni, érdemes lehet a partneri eseményeket használni.

### <a name="for-publishers"></a>Közzétevők számára

- Azt szeretné, hogy az események elérhetők legyenek az Azure-ban. A felhasználók a partneri témakörök és a saját és felügyelt esemény-előfizetések használatával szűrhetik és átadhatják ezeket az eseményeket. Más integrációs megközelítéseket is használhat, például [témákat](custom-topics.md) és [tartományokat](event-domains.md). Azonban nem teszik lehetővé az erőforrások (partneri témakörök) tiszta elkülönítését, valamint a kiadók és előfizetők közötti tulajdonjogot, felügyeletet és számlázást. Emellett ez a megközelítés intuitívabb felhasználói élményt nyújt, amely megkönnyíti a partneri témák felderítését és használatát.
- Az eseményeket egyetlen végpontra kívánja közzétenni, a névtér végpontját. És szeretné szűrni ezeket az eseményeket, hogy csak egy részhalmaz legyen elérhető. 
- A közzétett eseményekhez kapcsolódó metrikák láthatóságát szeretné megtekinteni.
- A [Felhőbeli események 1,0](https://cloudevents.io/) sémáját kívánja használni az eseményekhez.

### <a name="for-subscribers"></a>Előfizetőknek

- A [külső](#available-third-party-event-publishers) gyártóktól származó eseményekre szeretne előfizetni, és az eseményeket az Azure-ban vagy máshol található eseménykezelők használatával kezeli.
- Szeretné kihasználni az útválasztási funkciók és a [Célhelyek/](overview.md#event-handlers) eseménykezelők gazdag készletét, hogy feldolgozza az eseményeket a harmadik féltől származó forrásokból. 
- Olyan lazán összekapcsolt architektúrákat szeretne megvalósítani, amelyekben az előfizető/eseménykezelő nem ismeri a használt Message Broker létezését. 
- Egy rugalmas leküldéses kézbesítési mechanizmusra van szükség a Send-újrapróbálkozás támogatással és legalább egyszer szemantikagal.
- A [Felhőbeli események 1,0](https://cloudevents.io/) sémáját kívánja használni az eseményekhez. 


## <a name="available-third-party-event-publishers"></a>Elérhető külső gyártótól származó esemény-közzétevők
A harmadik féltől származó esemény-közzétevőnek egy bevezetési [folyamaton](partner-onboarding-overview.md) keresztül kell haladnia ahhoz, hogy az előfizető el tudja indítani az eseményeit. 

Ha előfizetője van, és szeretné, hogy egy harmadik féltől származó szolgáltatás a Event Gridon keresztül tegye elérhetővé az eseményeket 

### <a name="auth0"></a>Auth0
A **Auth0** az első elérhető partner közzétevője. Létrehozhat egy Auth0- [partneri témakört](auth0-overview.md) a Auth0 és az Azure-fiókok összekapcsolásához. Ez az integráció lehetővé teszi a Auth0 események valós idejű reagálását, naplózását és figyelését. A kipróbáláshoz lásd: [a Azure Event Grid integrálása a Auto0](auth0-how-to.md)

Ha azt szeretné, hogy egy harmadik féltől származó szolgáltatás a Event Gridon keresztül tegye elérhetővé az eseményeket, küldje el az ötletet a [felhasználói hang portálon](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Az esemény-közzétevők által kezelt erőforrások
Az Event Publisher a következő erőforrásokat hozza létre és kezeli:

### <a name="partner-registration"></a>Partnerregisztráció
A regisztráció a közzétevővel kapcsolatos általános információkat tartalmazza. Meghatározza a partneri témakört, amely akkor jelenik meg a Azure Portalban, amikor a felhasználók megpróbálnak létrehozni egy partneri témakört. A kiadó több partneri témakört is közzétehet, hogy megfeleljen az előfizetők igényeinek. Ez azt is megteheti, hogy a kiadó külön regisztrációkat (partneri témákat) hoz létre különböző szolgáltatásokból származó eseményekhez. Például az emberi erőforrások (HR) szolgáltatás esetében a közzétevő definiálhat egy partneri témakört olyan eseményekhez, mint például az alkalmazott, az alkalmazott, az alkalmazott pedig elhagyta a vállalatot. 

Tartsa szem előtt az alábbi szempontokat:

- Csak az Azure által jóváhagyott partner-regisztrációk láthatók. 
- A regisztrációk globálisak. Tehát nem egy adott Azure-régióhoz vannak társítva.
- A regisztráció egy opcionális erőforrás. Javasoljuk azonban, hogy (közzétevőként) hozzon létre egy regisztrációt. Lehetővé teszi, hogy a felhasználók felfedezzék a témaköröket a [Azure Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic) **partneri témakör létrehozása** oldalán. Ezután a felhasználó kiválaszthatja az események típusait (például az alkalmazottat, a bal oldali alkalmazottakat stb.) az esemény-előfizetések létrehozásakor.

### <a name="namespace"></a>Névtér
Az [Egyéni témakörökhöz](custom-topics.md) és [tartományokhoz](event-domains.md)hasonlóan a partneri névtér az események közzétételének regionális végpontja. Olyan névtereket mutat be, amelyekkel a közzétevők létrehozzák és kezelhetik az esemény-csatornákat. A névtér az Event channels tároló-erőforrásként is működik.

### <a name="event-channels"></a>Esemény-csatornák
Az Event Channel egy tükrözött erőforrás a partneri témakörben. Amikor egy közzétevő létrehoz egy Event csatornát a közzétevő Azure-előfizetésében, egy partneri témakört is létrehoz az előfizető Azure-előfizetésében. Az esemény-csatornán végrehajtott műveletek (kivéve a GET-t) a megfelelő előfizetői partner témakörre lesznek alkalmazva, még a törlés után is. Azonban csak a partneri témakörök olyan erőforrások, amelyeken az előfizetések és az események kézbesítése is konfigurálható.

## <a name="resources-managed-by-subscribers"></a>Előfizetők által felügyelt erőforrások 
Az előfizetők a közzétevő által definiált partneri témákat használhatják, és ez az egyetlen olyan típusú erőforrás, amelyet látnak és kezelnek. A partneri témakör létrehozása után az előfizető felhasználó létrehozhat olyan esemény-előfizetéseket, amelyek a szűrési szabályokat határozzák meg a [Célhelyek/eseménykezelők](overview.md#event-handlers)számára. Az előfizetőknek, a partnereknek és a kapcsolódó esemény-előfizetéseknek ugyanazok a funkciók, mint az [Egyéni témakörök](custom-topics.md) és a kapcsolódó előfizetések, amelyek egy jelentős különbséggel rendelkeznek: a partneri témák csak a [Cloud Events 1,0 sémát](cloudevents-schema.md)támogatják, amely a többi támogatott sémánál szélesebb körű képességeket biztosít.

Az alábbi képen a vezérlési sík műveleteinek folyamata látható.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="Partneri események – vezérlési sík folyamata":::

1. A közzétevő **partneri regisztrációt** hoz létre. A partnerek regisztrációi globálisak. Tehát nem egy adott Azure-régióhoz vannak társítva. Ez a lépés nem kötelező.
1. A közzétevő **partneri névteret** hoz létre egy adott régióban.
1. Ha az 1. előfizető megpróbál létrehozni egy partneri témakört, akkor először a közzétevő Azure-előfizetésében jön létre egy **Event Channel**, 1. esemény-csatorna.
1. Ezután létrejön egy **partneri témakör**, 1. partneri témakör, amely az előfizető Azure-előfizetésében jön létre. Az előfizetőnek aktiválnia kell a partneri témakört. 
1. Az 1. előfizető létrehoz egy **Azure Logic apps-előfizetést** az 1. partneri témakörben.
1. Az 1. előfizető **Azure Blob Storage-előfizetést** hoz létre az 1. partneri témakörben. 
1. Ha a 2. előfizető megpróbál létrehozni egy partneri témakört, egy másik **Event Channel**-et, az Event Channel 2-et, először a kiadó Azure-előfizetésében hozza létre. 
1. Ezután létrejön a **partneri témakör**, a 2. partneri témakör, amely a második előfizető Azure-előfizetésében jön létre. Az előfizetőnek aktiválnia kell a partneri témakört. 
1. A 2. előfizető létrehoz egy **Azure functions-előfizetést** a 2. partneri témakörben. 

## <a name="pricing"></a>Díjszabás
A partneri témaköröket a Event Grid használatakor végrehajtott műveletek száma alapján számítjuk fel. A számlázási és a részletes díjszabási információk alapján felhasználható összes típusú műveletről a [Event Grid díjszabását](https://azure.microsoft.com/pricing/details/event-grid/)ismertető témakörben talál további információt.

## <a name="limits"></a>Korlátok
A partneri témakörökre vonatkozó korlátokkal kapcsolatos részletes információkért tekintse meg [Event Grid szolgáltatási korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) .


## <a name="next-steps"></a>Következő lépések

- [Auth0-partneri témakör](auth0-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)
- [Legyen Event Grid partner](partner-onboarding-overview.md)