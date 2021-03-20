---
title: A Media Services API v3-ra való Migrálás előnyei
description: Ez a cikk a Media Services v2-ről v3-re való Migrálás előnyeit sorolja fel.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: b6d51e05598f60de0e9c8fb85472b7c14bba990e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598388"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>1. lépés – a Media Services API v3-re való Migrálás előnyeinek megismerése

![Áttelepítési útmutató emblémája](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![2. áttelepítési lépések](./media/migration-guide/steps-1.svg)

Javasoljuk, hogy a Azure Media Services V3 API 2020-05-01-es verzióját használja most az előnyök kinyeréséhez, mivel az új funkciók, funkciók és teljesítmény-optimalizálás csak az aktuális V3 API-ban érhető el.

Az API-verziót megváltoztathatja a portálon, a legújabb SDK-k, a legfrissebb parancssori felület és a REST API a megfelelő verziójú karakterlánc használatával.

A v3-as Media Services jelentős mértékben javult.  

## <a name="benefits-of-media-services-v3"></a>A Media Services v3 előnyei

| **V3 funkció** | **Előny** |
| --- | --- |
| **Azure Portal** | |
| Azure Portal frissítések | A Azure Portal úgy lett frissítve, hogy tartalmazza a V3 API-entitások kezelését. Lehetővé teszi az ügyfeleknek, hogy a portálon élő közvetítést indítsanak, hogyan küldhetnek v3 átalakítási feladatokat, kezelhetik a tartalomkezelési házirendeket, a streaming végpontokat, az API-hozzáférést, a társított Storage-fiókokat és a figyelési feladatokat. |
| **Fiókok és tárolók** | |
| Azure szerepköralapú hozzáférés-vezérlés (RBAC) | Az ügyfelek mostantól meghatározhatják saját szerepköreiket, és szabályozhatják az egyes entitásokhoz való hozzáférést a Media Services ARM API-ban. Ez segít a HRE-fiókok által az erőforrásokhoz való hozzáférés szabályozásában. |
| Felügyelt identitások | A felügyelt identitások nem teszik lehetővé a fejlesztők számára a hitelesítő adatok felügyeletét azáltal, hogy az Azure AD-ben identitást biztosítanak az Azure-erőforráshoz. [Itt](../../active-directory/managed-identities-azure-resources/overview.md)tekintheti meg a felügyelt identitások részleteit. |
| Privát hivatkozás támogatása | Az ügyfelek a VNet lévő PrivateEndpoint keresztül érhetik el Media Services végpontokat a Key Delivery, a LiveEvents és a StreamingEndpoints számára. |
| [Ügyfél-összekeveredésű kulcsok](concept-use-customer-managed-keys-byok.md) vagy saját kulcs használata (BYOK) támogatása | Az ügyfelek a Azure Key Vault egy kulcsával titkosítják a Media Services-fiókban lévő adataikat. |
| **Adategységek** | |
| Egy eszközhöz több [folyamatos átviteli lokátor](streaming-locators-concept.md) is tartozhat, amelyek különböző [dinamikus csomagolási](dynamic-packaging-overview.md) és dinamikus titkosítási beállításokkal rendelkeznek. | Az egyes eszközökön legfeljebb 100 streaming-lokátor engedélyezett. Az ügyfelek a médiatartalom egyetlen példányát tárolhatják az adategységben, de a különböző streaming-URL-címeket különböző streaming-házirendekkel vagy a célközönségen alapuló tartalomkezelési házirendekkel oszthatják meg.
| **Feladatok feldolgozása** ||
| A v3 bevezeti [](transforms-jobs-concept.md)a   fájl alapú feladatok feldolgozásának átalakítási koncepcióját. | Az átalakító használatával újrafelhasználható konfigurációk hozhatók létre, Azure Resource Manager sablonok hozhatók létre, és a feldolgozási beállítások elkülöníthetők több ügyfél vagy bérlő között. |
| A fájl alapú feladatok feldolgozásához a HTTP (S) URL-címet használhatja bemenetként. | Nincs szüksége az Azure-ban tárolt tartalomra, és nem kell bemeneti eszközöket létrehoznia. |
| **Élő események** ||
| Prémium szintű, 1080p élő események | Az új Live Event SKU lehetővé teszi, hogy az ügyfelek az akár 1080p felbontású Felhőbeli kódolást is igénybe vegyenek. |
| Új, [kis késleltetésű](live-event-latency.md) élő adatfolyam-támogatás élő eseményeken. | Így a felhasználók valós időben tekinthetik meg az élő eseményeket, mint ha ezt a beállítást nem engedélyezték. |
| Az élő esemény előzetes verziója támogatja a [dinamikus csomagolást](dynamic-packaging-overview.md)   és a dinamikus titkosítást. | Ez lehetővé teszi a tartalom védelmét az előnézet, a kötőjel és a HLS csomagolás esetében. |
| Élő kimenetek cseréje programok | Az élő kimenet használata egyszerűbb, mint a program entitás a v2 API-kon. |
| Az RTMP betöltése az élő eseményekhez továbbfejlesztett, és több kódoló támogatása | Növeli a stabilitást, és biztosítja a forrás-kódoló rugalmasságát. |
| Élő események stream nonstop | Élő eseményt üzemeltetheti, és megtarthatja a célközönséget hosszabb időszakokra is. |
| Élő átírás az élő eseményeken | Az élő átírás lehetővé teszi, hogy az ügyfelek valós időben automatikusan szövegbe írjanak a beszélt nyelveket az élő esemény szórása során. Ez jelentősen javítja az élő események hozzáférhetőségét. |
| [Készenléti üzemmód](live-events-outputs-concept.md#standby-mode) élő eseményeken | A készenléti állapotban lévő élő események kevésbé költségesek, mint az élő események futtatása. Ez lehetővé teszi az ügyfelek számára, hogy az élő események egy készletének megtartása mellett kevesebb időt is igénybe lehessen venni az élő események futtatására. A készenléti állapotú élő események csökkentett díjszabása február 2021-én lép érvénybe a legtöbb régióban, a többi pedig a 2021 áprilisban.
|**Tartalomvédelem** ||
| [Tartalomvédelem](content-key-policy-concept.md)   támogatja a többkulcsos funkciókat. | Az ügyfelek mostantól több tartalom titkosítási kulcsot is használhatnak a streaming-lokátorokban. |
| **Figyelés** | |
| [Azure EventGrid](monitoring/reacting-to-media-services-events.md) Notification-támogatás | A EventGrid-értesítések több funkciója is gazdag. Több típusú értesítés is létezik, szélesebb körű SDK-támogatás az értesítések saját alkalmazásban való fogadásához, valamint a meglévő Azure-szolgáltatások, amelyek eseménykezelőként működhetnek. |
| [Azure Monitor támogatás és integráció a Azure Portal](monitoring/monitor-events-portal-how-to.md) | Ez lehetővé teszi az ügyfeleknek, hogy megjelenítsék Media Services fiók kvótájának felhasználását, a valós idejű statisztikát a folyamatos átviteli végpontokról, valamint az élő események betöltésére és archiválására vonatkozó statisztikát. Az ügyfelek mostantól riasztásokat állíthatnak be, és valós idejű metrikus adatokon alapuló szükséges műveleteket hajthatnak végre. |

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]