---
title: Az Azure Signaler szolgáltatással kapcsolatos gyakori kérdések
description: Választ kaphat az Azure Signaler szolgáltatással kapcsolatos gyakori kérdésekre, beleértve a hibaelhárítást és a jellemző használati forgatókönyveket.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c65bc7e92d925f819a48fd8ab9a8160bc3eb72e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579303"
---
# <a name="azure-signalr-service-faq"></a>Azure Signaler szolgáltatás – gyakori kérdések

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Az Azure Signaler szolgáltatás készen áll éles használatra?

Igen, a [ASP.net Core jelző](https://dotnet.microsoft.com/apps/aspnet/signalr) és a [ASP.net-jelző](/aspnet/signalr/overview/getting-started/introduction-to-signalr) támogatása egyaránt általánosan elérhető.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Ha több alkalmazás-kiszolgáló is van, az összes kiszolgálónak vagy csak az egyiknek küldi az üzeneteket?

Az ügyfél és az alkalmazáskiszolgáló között egy-az-egyhez típusú hozzárendelés található. Az egyik ügyfélről érkező üzeneteket a rendszer mindig ugyanarra az alkalmazási kiszolgálóra továbbítja.

A leképezés mindaddig fenn van tartva, amíg az ügyfél vagy az alkalmazáskiszolgáló nem bontja a kapcsolatot.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Ha az egyik alkalmazás-kiszolgálóm nem érhető el, Hogyan találhatom meg és kaphatok értesítést?

Az Azure Signaler szolgáltatás figyeli a szívveréseket az alkalmazás-kiszolgálókról.
Ha a szívverések nem érkeznek meg egy megadott ideig, az alkalmazáskiszolgáló offline állapotba kerül. A rendszer leválasztja az ehhez az alkalmazáskiszolgáló számára leképezett összes ügyfélkapcsolatot.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Miért van az egyéni `IUserIdProvider` kivételem, ha ASP.net Core Signal SDK-ról az Azure signaler Service SDK-ra váltok?

A paraméter `HubConnectionContext context` különbözik a ASP.net Core signaler SDK és az Azure signaler Service SDK között, ha a `IUserIdProvider` hívása megtörténik.

ASP.NET Core a jelzőben a `HubConnectionContext context` fizikai ügyfélkapcsolat környezete az összes tulajdonság érvényes értékeivel.

Az Azure Signaler Service SDK-ban a `HubConnectionContext context` logikai ügyfél-kapcsolatok kontextusa. A fizikai ügyfél csatlakozik az Azure Signaler szolgáltatás példányához, így csak korlátozott számú tulajdonság van megadva.

Egyelőre csak a `HubConnectionContext.GetHttpContext()` és a `HubConnectionContext.User` hozzáféréshez érhető el.
[A forráskódot](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)megtekintheti.

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Konfigurálható az Azure Signaler szolgáltatásban elérhető átvitelek a kiszolgálóoldali oldalon ASP.NET Core jelzővel? Le lehet tiltani például a WebSocket-átvitelt?

Nem.

Az Azure Signaler szolgáltatás mindhárom olyan átvitelt biztosít, amelyet a ASP.NET Core a szignáló alapértelmezés szerint támogat. Nem konfigurálható. Az Azure Signaler szolgáltatás minden ügyfélkapcsolat esetében kezeli a kapcsolatokat és a szállításokat.

Az ügyféloldali átviteleket [ASP.net Core jelző konfigurációjában](/aspnet/core/signalr/configuration#configure-allowed-transports-1)dokumentált módon konfigurálhatja.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Mit jelent a metrikák, például az üzenetek száma vagy a Azure Portalban látható kapcsolatok száma? Milyen típusú összesítési típust válasszon?

A metrikák kiszámításának részleteit az [Azure signaler szolgáltatás üzeneteiben és kapcsolataiban](signalr-concept-messages-and-connections.md)tekintheti meg.

Az Azure Signaler szolgáltatás erőforrásainak áttekintés paneljén már kiválasztottuk a megfelelő összesítési típust. Ha a metrikák ablaktáblára lép, az összesítési típust használhatja hivatkozásként az [Azure Signal Service-ben lévő üzenetekhez és kapcsolatokhoz](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr) .

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Mit jelent a `Default` , a és a `Serverless` `Classic` szolgáltatás mód? Hogyan választhatok?

Az új alkalmazások esetében csak az alapértelmezett és a kiszolgáló nélküli módot kell használni. A fő különbség az, hogy rendelkezik-e olyan alkalmazás-kiszolgálókkal, amelyek kiszolgálói kapcsolatot létesít a szolgáltatással (azaz a `AddAzureSignalR()` szolgáltatáshoz való csatlakozáshoz használja). Ha igen, használja az alapértelmezett módot, ellenkező esetben kiszolgáló nélküli üzemmódot használjon.

A klasszikus mód a meglévő alkalmazások visszamenőleges kompatibilitására lett tervezve, ezért új alkalmazásokhoz nem használható.

További információ a [jelen dokumentum](concept-service-mode.md)szolgáltatási módból.

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Küldhetek üzenetet az ügyféltől kiszolgáló nélküli módban?

Ha a Signaler-példányban a felsőbb réteg beállítása lehetőséget választja, üzenetet küldhet az ügyféltől. A felsőbb réteg olyan végpontok halmaza, amelyek üzeneteket és kapcsolatfelvételi eseményeket fogadhatnak a Signaler szolgáltatástól. Ha nincs felsőbb réteg konfigurálva, a rendszer figyelmen kívül hagyja az ügyfél üzeneteit.

További információ az upstream-ról: [Ez a dokumentum](concept-upstream.md).

A felsőbb rétegbeli szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Az Azure Signaler szolgáltatás az ASP.NET-jelzővel való használatának eltérései vannak?

Az Azure Signaler szolgáltatás használatakor a ASP.NET-jelző egyes API-jai és funkciói nem támogatottak:
- Az ügyfelek és a központ (gyakran nevezik) tetszőleges állapotba való továbbításának lehetősége `HubState` nem támogatott.
- Az `PersistentConnection` osztály nem támogatott.
- A *Forever frame Transport* nem támogatott.
- Az Azure Signaler szolgáltatás már nem játssza le az ügyfélnek a kapcsolat nélküli üzemmódban küldött üzeneteket.
- Az Azure Signaler szolgáltatás használatakor a rendszer mindig az egyik ügyfél-kapcsolatok forgalmát irányítja át (más néven *Sticky*) az egyik app Server-példányra a csatlakozás időtartamára.

A ASP.NET-jelző támogatása a kompatibilitásra összpontosít, így nem minden új funkció támogatott a ASP.NET Core-jelzővel. A *MessagePack* és a *Streaming* például csak ASP.net Core signaler-alkalmazásokhoz érhető el.

Az Azure Signaler szolgáltatás különböző szolgáltatási módokhoz konfigurálható: `Classic` , `Default` és `Serverless` . A `Serverless` mód nem támogatott a ASP.net esetében. Az adatsík REST API is nem támogatott.

## <a name="where-does-my-data-reside"></a>Hol találhatók az adataim?

Az Azure Signaler szolgáltatás adatfeldolgozó szolgáltatásként működik. Nem tárol semmilyen vásárlói tartalmat, és az adattárolási szolgáltatás a kialakítás részét képezi. Ha az Azure Signaler szolgáltatást más Azure-szolgáltatásokkal, például az Azure Storage for Diagnostics szolgáltatással együtt használja, tekintse meg [ezt](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) a tanulmányt, amely útmutatást nyújt az adattárolás Azure-régiókban való megőrzéséhez.