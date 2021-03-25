---
title: Rugalmasság és vész-helyreállítás az Azure Signaler szolgáltatásban
description: A többszörös szignáló szolgáltatási példányok beállításának áttekintése a rugalmasság és a katasztrófa utáni helyreállítás érdekében
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: 996fa53aa105c0bcc27db7134c25d6d00e542a78
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110287"
---
# <a name="resiliency-and-disaster-recovery-in-azure-signalr-service"></a>Rugalmasság és vész-helyreállítás az Azure Signaler szolgáltatásban

A rugalmasság és a vész-helyreállítás gyakran szükséges az online rendszerekhez. Az Azure Signaler szolgáltatás már garantálja a 99,9%-os rendelkezésre állást, de továbbra is regionális szolgáltatás.
A szolgáltatási példány mindig egy régióban fut, és nem lesz feladatátvétel egy másik régióba, ha az egész régióra kiterjedő leállás van.

A Service SDK Ehelyett a több szignáló szolgáltatás példányainak támogatását teszi lehetővé, és automatikusan átvált más példányokra, ha ezek némelyike nem érhető el.
Ezzel a szolgáltatással helyreállíthatja a vészhelyzetek időpontját, de saját maga is be kell állítania a megfelelő rendszertopológiát. Ebből a dokumentumból megtudhatja, hogyan teheti meg ezt a dokumentumot.

## <a name="high-available-architecture-for-signalr-service"></a>Magas rendelkezésre állású architektúra a Signaler szolgáltatáshoz

Ha régiók közötti rugalmasságot szeretne biztosítani a Signaler szolgáltatáshoz, több szolgáltatási példányt kell beállítania különböző régiókban. Tehát ha az egyik régió nem érhető el, a többiek biztonsági mentésként is használhatók.
Ha több szolgáltatási példányt csatlakoztat az App Serverhez, két szerepkör van, az elsődleges és a másodlagos.
Az elsődleges az a példány, amely online forgalomban van, és a másodlagos egy teljesen működőképes, de elsődleges biztonsági mentési példány.
Az SDK implementációjában az egyeztetés csak az elsődleges végpontokat fogja visszaadni, így a normál esetben az ügyfelek csak az elsődleges végpontokhoz csatlakoznak.
Ha azonban az elsődleges példány nem áll le, az egyeztetés a másodlagos végpontokat fogja visszaadni, hogy az ügyfél továbbra is kapcsolatokat hozzon.
Az elsődleges példány és az alkalmazáskiszolgáló normál kiszolgálói kapcsolatokon keresztül csatlakozik, de a másodlagos példány és az alkalmazáskiszolgáló a gyenge kapcsolat nevű speciális kapcsolaton keresztül csatlakoznak.
A gyenge kapcsolatok fő különbsége az, hogy nem fogadja el az Ügyfélkapcsolat-útválasztást, mert a másodlagos példány egy másik régióban található. Az ügyfél útválasztása egy másik régióba nem optimális választás (a késés növelése).

Az egyik szolgáltatási példány különböző szerepkörökkel rendelkezhet, ha több alkalmazáshoz csatlakozik.
A régiók közötti forgatókönyvek egyik tipikus beállítása, hogy két (vagy több) jel a Signaler Service instances és az App Server.
A pair app Serveren és a Signaler szolgáltatáson belül ugyanabban a régióban találhatók, a Signaler-szolgáltatás pedig elsődleges szerepkörként csatlakozik az alkalmazás-kiszolgálóhoz.
Az egyes párok az App Server és a Signaler szolgáltatás között is csatlakoztatva vannak, de a jelző másodlagos lesz, ha másik régióban lévő kiszolgálóhoz csatlakozik.

Ezzel a topológiával az egyik kiszolgálóról érkező üzenet továbbra is továbbítható minden ügyfélnek, mivel az összes App-kiszolgáló és a Signaler szolgáltatás példányai összekapcsolódnak egymással.
Ha azonban az ügyfél csatlakoztatva van, az optimális hálózati késés érdekében mindig az azonos régióban található app Serverre irányítja a rendszer.

Alább látható egy diagram, amely a következő topológiát szemlélteti:

![A diagram két olyan régiót mutat be, amelyek mindegyike egy alkalmazás-kiszolgálóval és egy Signaler szolgáltatással rendelkezik, ahol az egyes kiszolgálók a régiójában elsődlegesként és a másik régióban lévő szolgáltatással társítva vannak.](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-multiple-signalr-service-instances"></a>Több Signaler szolgáltatás példányának konfigurálása

Az App-kiszolgálókon és a Azure Functionson több szignáló szolgáltatási példány is támogatott.

Miután létrehozta a Signaler szolgáltatást és az App Servers/Azure Functions az egyes régiókban, beállíthatja, hogy az alkalmazás-kiszolgálók/Azure Functions az összes Signaler szolgáltatás példányaihoz kapcsolódjon.

### <a name="configure-on-app-servers"></a>Konfigurálás az alkalmazás-kiszolgálókon
Kétféle módon teheti meg:

#### <a name="through-config"></a>Konfiguráción keresztül

Meg kell tudnia, hogyan állíthatja be a Signal Service-kapcsolati karakterláncot a környezeti változók/Alkalmazásbeállítások/web. cofig használatával egy nevű konfigurációs bejegyzésben `Azure:SignalR:ConnectionString` .
Ha több végponttal rendelkezik, a következő formátumban állíthatja be őket több konfigurációs bejegyzésbe:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Itt `<name>` látható a végpont neve, és a `<role>` szerepköre (elsődleges vagy másodlagos).
A név nem kötelező, de akkor is hasznos, ha tovább szeretné testreszabni az útválasztási viselkedést több végpont között.

#### <a name="through-code"></a>Kód használatával

Ha a kapcsolódási karakterláncokat inkább valahol máshol szeretné tárolni, akkor a kódban is elolvashatja őket, és paraméterként használhatja őket a `AddAzureSignalR()` (ASP.net Core) vagy `MapAzureSignalR()` (ASP.net) hívásakor.

A mintakód a következő:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

Több elsődleges vagy másodlagos példányt is beállíthat. Ha több elsődleges és/vagy másodlagos példány is van, akkor az egyeztetés a következő sorrendben ad vissza egy végpontot:

1. Ha legalább egy elsődleges példány online állapotban van, egy véletlenszerű elsődleges online példányt ad vissza.
2. Ha az összes elsődleges példány nem érhető el, egy véletlenszerű másodlagos online példányt ad vissza.

### <a name="configure-on-azure-functions"></a>Konfigurálás Azure Functions
Tekintse meg [ezt a cikket](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/docs/sharding.md#configuration-method).

## <a name="failover-sequence-and-best-practice"></a>Feladatátvételi folyamat és ajánlott eljárás

Most már rendelkezik a megfelelő rendszertopológia-beállítással. Ha az egyik szignáló szolgáltatási példánya nem érhető el, az online forgalom más példányokra lesz irányítva.
A következő történik, ha egy elsődleges példány leáll (és néhány idő elteltével helyreállítja őket):

1. Az elsődleges szolgáltatás példánya nem érhető el, az ezen a példányon lévő összes kiszolgálói kapcsolat el lesz dobva.
2. Az ehhez a példányhoz csatlakoztatott összes kiszolgáló offline állapotba kerül, és az egyeztetés leállítja a végpont visszaadását, és megkezdi a másodlagos végpont visszaküldését.
3. Az összes ügyfélkapcsolat ezen a példányon is be lesz zárva, az ügyfelek újra fognak csatlakozni. Mivel az alkalmazás-kiszolgálók most másodlagos végpontot adnak vissza, az ügyfelek a másodlagos példányhoz fognak csatlakozni.
4. A másodlagos példány most az összes online forgalmat elvégzi. A kiszolgálóról az ügyfelekre irányuló összes üzenet továbbra is továbbítható, mert a másodlagos kapcsolat az összes app Serverhez csatlakozik. Az ügyfél és a kiszolgáló közötti üzenetküldés azonban csak az azonos régióban található app Serverre irányítja.
5. Az elsődleges példány visszaállítása és online állapotba állítása után az App Server újra létrehozza a kapcsolatokat, és megjelöli azt online állapotba. Az egyeztetés most újra visszaküldi az elsődleges végpontot, így az új ügyfelek visszaállnak az elsődleges rendszerbe. A meglévő ügyfelek azonban nem lesznek elvetve, és a rendszer továbbra is a másodlagosra irányítja, amíg le nem bontja őket.

Az alábbi diagramok azt szemléltetik, hogyan történik a feladatátvétel a Signaler szolgáltatásban:

Feladatátvétel előtt 1. ábra. ![](media/signalr-concept-disaster-recovery/before-failover.png)

2. ábra ![ a feladatátvétel utáni feladatátvétel után](media/signalr-concept-disaster-recovery/after-failover.png)

3. ábra rövid idő elteltével az elsődleges helyreállítások után ![ rövid idő elteltével](media/signalr-concept-disaster-recovery/after-recover.png)

Normál esetben a csak az elsődleges app Server és a Signaler szolgáltatás rendelkezik online forgalommal (kék színnel).
A feladatátvétel után a másodlagos app Server és a Signaler szolgáltatás is aktívvá válik.
Miután az elsődleges jelző szolgáltatás ismét online állapotba került, az új ügyfelek csatlakozni fognak az elsődleges jelzőhöz. A meglévő ügyfelek azonban még mindig másodlagos kapcsolattal csatlakoznak, így mindkét példány rendelkezik forgalommal.
Az összes meglévő ügyfél leválasztása után a rendszer visszatér a normális kerékvágásba (1. ábra).

A régiók közötti magas rendelkezésre állású architektúrák megvalósításának két fő mintája létezik:

1. Az első az, hogy az App Server és a Signaler szolgáltatás egy pár példánya az összes online forgalomra vonatkozik, és van egy másik párja (aktív/passzív, amely az 1. ábrán látható). 
2. A másik az, hogy két (vagy több) pár app Servert és a Signaler szolgáltatás példányát kell használnia, amelyek mindegyike az online forgalom részét képezi, és biztonsági mentésként szolgál más pároknak (az aktív/aktív, a 3. ábrához hasonlóan).

A signaler szolgáltatás mindkét mintát támogathatja, a fő különbség az, hogy hogyan valósítja meg az alkalmazás-kiszolgálókat.
Ha az alkalmazás-kiszolgálók aktívak/passzívak, a Signaler szolgáltatás aktív/passzív lesz (mivel az elsődleges alkalmazás-kiszolgáló csak az elsődleges jelző szolgáltatás példányát adja vissza).
Ha az App Servers aktív/aktív, a Signaler szolgáltatás aktív/aktív lesz (mivel az összes alkalmazás-kiszolgáló visszaküldi a saját elsődleges jelző példányait, így ezek mindegyike lekérheti a forgalmat).

Ha a használni kívánt mintákat nem számítja ki, akkor az egyes szignáló szolgáltatás-példányokat elsődlegesként kell összekötnie egy alkalmazás-kiszolgálóval.

A Signaler-kapcsolatok jellegéből adódóan (ez egy hosszú csatlakozás), az ügyfelek a kapcsolódást akkor fogják tapasztalni, ha katasztrófa és feladatátvétel történik.
Ezeket az eseteket az ügyféloldali oldalon kell kezelnie ahhoz, hogy transzparensek legyenek a végfelhasználók számára. Tegyük fel például, hogy újracsatlakozik a kapcsolat lezárása után.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja az alkalmazást, hogy rugalmasságot biztosítson a Signaler szolgáltatás számára. Ha többet szeretne megtudni a kiszolgálóról/az ügyfél kapcsolatáról és a kapcsolati útválasztásról a Signaler szolgáltatásban, olvassa el [ezt a cikket](signalr-concept-internals.md) a signaler szolgáltatás belső szolgáltatásaihoz.

Olyan méretezési forgatókönyvek esetében, mint például a horizontális skálázás, amely több példányt használ a nagy számú kapcsolat kezelésére, olvassa el [a több példány skálázása című témakört](signalr-howto-scale-multi-instances.md).

További információ a Azure Functions több szignáló szolgáltatásbeli példányokkal való konfigurálásáról: az [Azure signaler szolgáltatás példányainak támogatása Azure functions](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/docs/sharding.md).
