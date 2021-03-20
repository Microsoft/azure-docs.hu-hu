---
title: Szolgáltatási mód az Azure Signaler szolgáltatásban
description: Az Azure Signaler szolgáltatás különböző szolgáltatási módjainak áttekintése, a különbségek és a vonatkozó felhasználói forgatókönyvek ismertetése
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143262"
---
# <a name="service-mode-in-azure-signalr-service"></a>Szolgáltatási mód az Azure Signaler szolgáltatásban

A szolgáltatási mód az Azure Signaler szolgáltatás egyik fontos fogalma. Új jelző-erőforrás létrehozásakor a rendszer kérni fogja a szolgáltatási mód megadását:

:::image type="content" source="media/concept-service-mode/create.png" alt-text="Szolgáltatás mód kiválasztása létrehozáskor":::

Később is megváltoztathatja a beállítások menüben:

:::image type="content" source="media/concept-service-mode/update.png" alt-text="Szolgáltatási mód frissítése":::

Az Azure Signaler szolgáltatás jelenleg három szolgáltatási módot támogat: **alapértelmezett**, **kiszolgáló** nélküli és **klasszikus**. A Signaler-erőforrás különbözőképpen viselkedik a különböző módokon. Ebből a cikkből megtudhatja, hogy milyen különbségek vannak, és hogyan választhatja ki a megfelelő szolgáltatási módot a forgatókönyv alapján.

## <a name="default-mode"></a>Alapértelmezett mód

Új jelző-erőforrás létrehozásakor az alapértelmezett mód az alapértelmezett érték a szolgáltatási mód számára. Ebben a módban az alkalmazás egy tipikus ASP.NET Core (vagy ASP.NET) jelző alkalmazásként működik, ahol olyan webkiszolgálóval rendelkezik, amely egy központot (a továbbiakban: központi kiszolgáló) üzemeltet, és az ügyfelek kétirányú, valós idejű kommunikációt használhatnak a központi kiszolgálóval. Az egyetlen különbség az ügyfél és a kiszolgáló közvetlen csatlakoztatása, az ügyfél és a kiszolgáló egyaránt a Signaler szolgáltatáshoz való csatlakozás és a szolgáltatás proxyként való használata. Alább látható egy diagram, amely az alapértelmezett üzemmódban mutatja be a tipikus alkalmazási struktúrát:

:::image type="content" source="media/concept-service-mode/default.png" alt-text="Az alkalmazás szerkezete az alapértelmezett módban":::

Tehát ha van egy Signaler-alkalmazás, és integrálni szeretné a Signaler szolgáltatással, a legtöbb esetben az alapértelmezett módot kell választania.

### <a name="connection-routing-in-default-mode"></a>A kapcsolatok útválasztása alapértelmezett módban

Alapértelmezett módban a központi kiszolgáló és a Signaler szolgáltatás (kiszolgáló-kapcsolatok) közötti WebSocket-kapcsolatok lesznek elérhetők. Ezek a kapcsolatok az üzenetek kiszolgáló és ügyfél közötti átvitelére szolgálnak. Amikor új ügyfél csatlakozik, a Signaler szolgáltatás az ügyfelet egy központi kiszolgálóra irányítja (feltételezi, hogy több kiszolgálóval rendelkezik) a meglévő kiszolgálói kapcsolatokon keresztül. Az ügyfél kapcsolata az élettartama alatt ugyanazon a központi kiszolgálón fog maradni. Amikor az ügyfél üzeneteket küld, mindig ugyanarra a központi kiszolgálóra kerül. Ezzel a viselkedéssel biztonságosan megtarthat néhány állapotot a központi kiszolgálón található egyes kapcsolatokhoz. Ha például a kiszolgáló és az ügyfél között szeretne adatfolyamot továbbítani, nem kell figyelembe vennie azt az esetet, amikor az adatcsomagok különböző kiszolgálókra mennek.

> [!IMPORTANT]
> Ez azt is jelenti, hogy az alapértelmezett üzemmódú ügyfél nem tud kapcsolódni a kiszolgáló csatlakoztatása nélkül. Ha a hálózati megszakítás vagy a kiszolgáló újraindítása miatt az összes hub-kiszolgáló le van választva, az ügyfél-csatlakozás hibaüzenetet kap, amely arról tájékoztat, hogy nincs kiszolgáló csatlakoztatva. Ezért az Ön felelőssége, hogy a Signaler szolgáltatáshoz legalább egy központi kiszolgálót csatlakoztatjon (például több központi kiszolgálóval rendelkezik, és gondoskodjon arról, hogy ne lépjenek offline állapotba olyan dolgok esetében, mint például a karbantartás).

Ez az útválasztási modell azt is jelenti, hogy amikor egy központi kiszolgáló offline állapotba kerül, a kiszolgáló eldobása megtörténik. Ezért a kapcsolat eldobása akkor várható, ha a központi kiszolgáló karbantartás céljából offline állapotban van, és megfelelően kezeli az újracsatlakozást, hogy az ne legyen negatív hatással az alkalmazására.

## <a name="serverless-mode"></a>Kiszolgáló nélküli mód

A kiszolgáló nélküli mód, ahogy a neve is jelenti, egy olyan mód, amely nem rendelkezhet központi kiszolgálóval. Az alapértelmezett mód összehasonlításával ebben az üzemmódban az ügyfél nem igényli, hogy a hub Server csatlakoztatva legyen. Minden kapcsolat "kiszolgáló nélküli" módban van csatlakoztatva a szolgáltatáshoz, és a szolgáltatás felelős az ügyfélkapcsolatok kezeléséhez, például az ügyféloldali pingelések kezeléséhez (az alapértelmezett módban ezt a hub-kiszolgálók kezelik).

Ebben a módban nincs kiszolgálói kapcsolat (ha a Service SDK-t a kiszolgálói kapcsolat létesítésére próbálja használni, hibaüzenet jelenik meg). Ezért nincs szükség a kapcsolatok útválasztására és a kiszolgáló-ügyfél stickiion-re (az alapértelmezett üzemmód szakaszban leírtak szerint). Azonban továbbra is használhat kiszolgálóoldali alkalmazást az üzenetek ügyfeleknek való leküldéséhez. Ezt kétféleképpen teheti meg, ha [REST API-kat](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) használ egyszeri küldéshez vagy WebSocket-kapcsolaton keresztül, hogy hatékonyabban tudjon több üzenetet küldeni (vegye figyelembe, hogy ez a WebSocket-kapcsolat eltér a kiszolgálói kapcsolattól).

> [!NOTE]
> A REST API és a WebSocket mód is támogatott a Signal Service [Management SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)-ban. Ha a .NET-től eltérő nyelvet használ, az ezt a [specifikációt](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)követő REST API-kat manuálisan is meghívhatja.
>
> Ha Azure Functions használ, az üzenetek kimeneti Kötésként való elküldéséhez a [signaler szolgáltatás kötéseit használhatja a Azure Functionshoz](../azure-functions/functions-bindings-signalr-service.md) (a továbbiakban a függvény kötése).

Az is lehetséges, hogy a kiszolgálói alkalmazás üzeneteket és kapcsolódási eseményeket fogad az ügyfelektől. A szolgáltatás a webhookok használatával továbbítja az üzeneteket és a kapcsolódási eseményeket az előre konfigurált végpontoknak (felsőbb rétegnek nevezik). Az alapértelmezett üzemmódhoz képest nincs garancia a stickie-re, és a HTTP-kérelmek kevésbé hatékonyak, mint a WebSocket-kapcsolatok.

A felsőbb réteg konfigurálásával kapcsolatos további információkért tekintse meg ezt a [dokumentációt](./concept-upstream.md).

Alább látható egy diagram, amely bemutatja, hogyan működik a kiszolgáló nélküli üzemmód:

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="Alkalmazás szerkezete kiszolgáló nélküli módban":::

> [!NOTE]
> Vegye figyelembe, hogy az alapértelmezett módban REST API/Management SDK/Function kötést is használhat, hogy közvetlenül üzeneteket küldjön az ügyfeleknek, ha nem szeretne a hub-kiszolgálón átlépni. Az alapértelmezett üzemmódú ügyfélkapcsolatokat azonban továbbra is a hub-kiszolgálók kezelik, és a felsőbb rétegbeli kapcsolat nem fog működni ebben a módban.

## <a name="classic-mode"></a>Klasszikus mód

A klasszikus az alapértelmezett és a kiszolgáló nélküli üzemmód vegyes módja. Ebben a módban a kapcsolati módot úgy kell megállapítani, hogy van-e a hub-kiszolgáló csatlakoztatva az Ügyfélkapcsolat létrehozásakor. Ha van központi kiszolgáló, az ügyfélkapcsolatot egy központi kiszolgálóra irányítja a rendszer. Ellenkező esetben olyan kiszolgáló nélküli módba lép, amelyben az ügyfél és a kiszolgáló közötti üzenet nem továbbítható a központi kiszolgálónak. Ez bizonyos eltéréseket okoz, például ha az összes központi kiszolgáló rövid ideig nem érhető el, az ebben az időszakban létrehozott összes ügyfélkapcsolat kiszolgáló nélküli módban lesz, és nem tud üzeneteket küldeni a központi kiszolgálónak.

> [!NOTE]
> A klasszikus mód elsősorban a korábban létrehozott alkalmazások visszamenőleges kompatibilitásához szükséges alapértelmezett és kiszolgáló nélküli mód. Javasoljuk, hogy többé ne használja ezt a módot. Új alkalmazások esetén válassza az alapértelmezett vagy a kiszolgáló nélküli lehetőséget a forgatókönyv alapján. A meglévő alkalmazások esetében javasoljuk, hogy tekintse át a használati eseteket, és válassza ki a megfelelő szolgáltatási módot.

A klasszikus mód nem támogatja az olyan új szolgáltatásokat, mint az upstream kiszolgáló nélküli módban.

## <a name="choose-the-right-service-mode"></a>Válassza ki a megfelelő szolgáltatási módot

Most meg kell ismernie a szolgáltatási módok közötti különbségeket, és tudnia kell, hogyan választhat. Ahogy az előző szakaszban már megtanultuk, a klasszikus mód nem javasolt, és csak az alapértelmezett és a kiszolgáló nélküli beállítást kell választania. Íme néhány további tipp, amely segítséget nyújt az új alkalmazások helyes választásához és a meglévő alkalmazások klasszikus üzemmódjának kivonásához.

* Ha már ismeri a jeladó függvénytár működését, és szeretné áthelyezni a saját üzemeltetésű jelzőt az Azure Signaler szolgáltatás használatára, válassza az alapértelmezett mód lehetőséget. Az alapértelmezett mód pontosan ugyanúgy működik, mint a saját üzemeltetésű jelző (és ugyanazt a programozási modellt használhatja a jelzőlámpa Library-ben), a Signaler szolgáltatás csak az ügyfelek és a központi kiszolgálók közötti proxyként működik.

* Ha új alkalmazást hoz létre, és nem kívánja fenntartani a központi kiszolgáló és a kiszolgáló kapcsolatait, válassza a kiszolgáló nélküli mód lehetőséget. Ez a mód általában a Azure Functions együtt működik, így nem kell minden kiszolgálót fenntartania. Továbbra is használhatja a duplex kommunikációt (REST API/Management SDK/Function kötés + felsőbb réteggel), de a programozási modell nem fog megjelenni, mint a Signal Library.

* Ha az ügyfélkapcsolatokat és a háttérbeli alkalmazásokat mindkét központi kiszolgálóval szeretné leküldeni az ügyfeleknek (például REST API), akkor továbbra is az alapértelmezett módot kell választania. Ne feledje, hogy az alapértelmezett és a kiszolgáló nélküli mód közötti fő különbség az, hogy rendelkezik-e központi kiszolgálókkal, és hogy az ügyfélkapcsolatok hogyan irányíthatók. A REST API/Management SDK/Function kötés mindkét módban használható.

* Ha például valóban van egy vegyes forgatókönyve, például két különböző hub van ugyanazon a jelző erőforráson, az egyiket hagyományos jelző központként használják, a másik pedig a Azure Functions, és nem rendelkezik a központi kiszolgálóval, érdemes elkülöníteni őket két, az alapértelmezett és a kiszolgáló nélküli módban lévő Signal-erőforrásnak.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az alapértelmezett és kiszolgáló nélküli mód használatáról, olvassa el a következő cikkeket:

* [Az Azure SignalR szolgáltatás belső elemei](signalr-concept-internals.md)

* [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](signalr-concept-serverless-development-config.md)