---
title: Hibaelhárítás Azure Cosmos DB HTTP 408 vagy időtúllépési kérelmek a .NET SDK-val kapcsolatban
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja a .NET SDK-kérelmek időtúllépési kivételeit.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8d35f7c666562022f503b2777f30f84193d0231
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440003"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>A .NET SDK-kérelmek időtúllépési kivételeinek diagnosztizálása és megoldása Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A HTTP 408 hiba akkor fordul elő, ha az SDK nem tudta befejezni a kérelmet az időkorlát lejárta előtt.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Az időtúllépés testreszabása a Azure Cosmos DB .NET SDK-ban

Az SDK két különböző alternatívát tartalmaz az időtúllépések szabályozására, amelyek mindegyike eltérő hatókörrel rendelkezik.

### <a name="requesttimeout"></a>RequestTimeout

Az `CosmosClientOptions.RequestTimeout` (vagy `ConnectionPolicy.RequestTimeout` az SDK v2) konfigurálásával az egyes hálózati kérelmeket érintő időtúllépést állíthat be. Egy felhasználó által indított művelet több hálózati kérésre is kiterjedhet (például lehetséges a szabályozás). Ezt a konfigurációt minden hálózati kérelem esetében alkalmazni kell az Újrapróbálkozás után. Ez az időkorlát nem egy végpontok közötti műveleti kérelem időtúllépése.

### <a name="cancellationtoken"></a>CancellationToken

Az SDK-ban az összes aszinkron művelethez választható CancellationToken paraméter tartozik. Ez a [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) paraméter a teljes műveletben, az összes hálózati kérelemben használatos. A hálózati kérelmek között előfordulhat, hogy a lemondási jogkivonat be van jelölve, és egy művelet meg lett szakítva, ha a kapcsolódó jogkivonat lejárt. A lemondási tokent a művelet hatókörében várhatóan várt időkorlát meghatározására kell használni.

> [!NOTE]
> A `CancellationToken` paraméter egy olyan mechanizmus, amelyben a könyvtár a megszakítást fogja ellenőriznie, ha [nem okoz érvénytelen állapotot](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Előfordulhat, hogy a művelet nem szakítja meg pontosan, ha a megszakítás során megadott idő fel van állítva. Ehelyett az idő letelte után megszakítja a biztonsági mentést.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Az alábbi lista a kérelmek időtúllépési kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="high-cpu-utilization"></a>Magas processzorkihasználtság
A leggyakoribb eset a processzor magas kihasználtsága. Az optimális késés érdekében a CPU-használatnak nagyjából 40%-nak kell lennie. A maximális (nem átlagos) CPU-kihasználtság figyeléséhez használja a 10 másodperces értéket. A CPU-tüskék gyakoribbak a több partíciós lekérdezések esetében, ahol több kapcsolat is lehetséges egyetlen lekérdezéshez.

Ha a hiba `TransportException` információt tartalmaz, a következőket is tartalmazhatja `CPU History` :

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Ha a CPU-mérések 70%-nál nagyobbak, az időtúllépést valószínűleg a CPU-kimerültség okozhatja. Ebben az esetben a megoldás a magas CPU-kihasználtság forrásának vizsgálata és a kihasználtság csökkentése, vagy a gép felskálázása egy nagyobb erőforrás-méretre.
* Ha a CPU mérése nem történik meg 10 másodpercenként (pl. a hézagok vagy a mérési idők alapján több idő telik el a mérések között), akkor az ok a szál erőforráshiánya. Ebben az esetben a megoldás a szál erőforráshiányát (esetlegesen zárolt szálakat) előidéző ok kivizsgálása, vagy a gép(ek) felskálázása egy nagyobb erőforrás-méretre.

#### <a name="solution"></a>Megoldás:
Az SDK-t használó ügyfélalkalmazás vertikálisan fel-vagy kibővíthető.

### <a name="socket-or-port-availability-might-be-low"></a>Lehet, hogy a szoftvercsatorna vagy a port rendelkezésre állása alacsony
Az Azure-ban való futtatáskor a .NET SDK-t használó ügyfelek elérhetik az Azure SNAT (PAT) portjának kimerülését.

#### <a name="solution-1"></a>1. megoldás:
Ha Azure-beli virtuális gépeken fut, kövesse a [SNAT-portok kimerülésének útmutatóját](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>2. megoldás:
Ha Azure App Serviceon fut, kövesse a [csatlakoztatási hibák hibaelhárítási útmutatóját](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) , és [használja a app Service diagnosztikát](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>3. megoldás:
Ha Azure Functionson fut, ellenőrizze, hogy az összes érintett szolgáltatáshoz (beleértve a Azure Cosmos DB) tartozó egyedi vagy statikus ügyfelek karbantartására vonatkozó [Azure functions javaslatot](../azure-functions/manage-connections.md#static-clients) követi-e. A [szolgáltatási korlátokat](../azure-functions/functions-scale.md#service-limits) a függvényalkalmazás üzemeltetésének típusa és mérete alapján vizsgálja meg.

#### <a name="solution-4"></a>4. megoldás:
Ha HTTP-proxyt használ, győződjön meg arról, hogy az képes támogatni az SDK-ban konfigurált kapcsolatok számát `ConnectionPolicy` . Ellenkező esetben a csatlakoztatási problémákkal szembesül.

### <a name="create-multiple-client-instances"></a>Több ügyfél-példány létrehozása
Több ügyfél-példány létrehozása a kapcsolati problémákhoz és időtúllépéshez vezethet.

#### <a name="solution"></a>Megoldás:
Kövesse a [teljesítménnyel kapcsolatos tippeket](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage), és használjon egyetlen CosmosClient-példányt egy teljes folyamaton belül.

### <a name="hot-partition-key"></a>Gyors partíciós kulcs
A Azure Cosmos DB a teljes kiosztott átviteli sebességet egyenletesen osztja el a fizikai partíciók között. Gyakori partíció esetén a fizikai partíciók egy vagy több logikai partíciója a fizikai partíciók másodpercenkénti kérelmi egységét (RU/s) is felhasználja. Ugyanakkor a többi fizikai partíción lévő RU/s nem lesz használatban. Ez azt eredményezi, hogy a felhasznált RU/s mennyisége kevesebb lesz, mint az adatbázis vagy a tároló összesített kiépített RU/s-száma, de továbbra is megtekintheti a szabályozást (429s) a gyors logikai partíciós kulcsra vonatkozó kérelmeknél. A [normalizált ru](monitor-normalized-request-units.md) -használati metrika használatával ellenőrizze, hogy a számítási feladatok egy gyors partícióval találkoznak-e. 

#### <a name="solution"></a>Megoldás:
Válasszon egy jó partíciós kulcsot, amely egyenletesen osztja el a kérelmek mennyiségét és tárolását. Megtudhatja, hogyan [módosíthatja a partíciós kulcsot](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Magas fokú párhuzamosság
Az alkalmazás magas szintű párhuzamosságot eredményez, ami a csatornán való versenyhez vezethet.

#### <a name="solution"></a>Megoldás:
Az SDK-t használó ügyfélalkalmazás vertikálisan fel-vagy kibővíthető.

### <a name="large-requests-or-responses"></a>Nagyméretű kérelmek vagy válaszok
A nagyméretű kérelmek vagy válaszok a csatornán kívüli blokkolást okozhatnak a csatornán, és súlyosbítják a versengés mértékét, akár viszonylag alacsony fokú párhuzamosságtal is.

#### <a name="solution"></a>Megoldás:
Az SDK-t használó ügyfélalkalmazás vertikálisan fel-vagy kibővíthető.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>A meghibásodási arány a Azure Cosmos DB SLA-n belül van
Az alkalmazásnak képesnek kell lennie az átmeneti hibák kezelésére, és szükség esetén újra kell próbálkoznia. A rendszer nem próbálkozik újra a 408-es kivételekkel, mert a létrehozási útvonalakon nem lehet tudni, hogy a szolgáltatás hozta-e létre az adott tételt. Ha ugyanezt az elemeket újra elküldi a létrehozáshoz, a rendszer ütközést okoz. A felhasználói alkalmazások üzleti logikájának egyéni logikája lehet az ütközések kezeléséhez, ami egy meglévő elem és a Create újrapróbálkozás közötti ütközés miatt megszakadhat.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>A meghibásodási arány sérti a Azure Cosmos DB SLA-t
Forduljon az [Azure ügyfélszolgálatához](https://aka.ms/azure-support).

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.