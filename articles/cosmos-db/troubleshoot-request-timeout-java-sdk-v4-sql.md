---
title: Hibaelhárítás Azure Cosmos DB HTTP 408 vagy időtúllépési hibák a Java v4 SDK-val
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja a Java SDK-kérelmek időtúllépési kivételeit a Java v4 SDK-val.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a805300ac62d0627c9b06188c9764a6887947afe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411286"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Az Azure Cosmos DB Java v4 SDK-kérelmek időtúllépési kivételeinek diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A HTTP 408 hiba akkor fordul elő, ha az SDK nem tudta befejezni a kérelmet az időkorlát lejárta előtt.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Az alábbi lista a kérelmek időtúllépési kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="existing-issues"></a>Meglévő problémák
Ha úgy látja, hogy a kérések hosszabb ideig tartanak, vagy gyakrabban jelentkeznek az időzítéssel, frissítse a Java v4 SDK-t a legújabb verzióra. Megjegyzés: javasoljuk, hogy a 4.7.0 és újabb verziót használja. További részletekért tekintse meg a [Java v4 SDK kibocsátási megjegyzéseit](sql-api-sdk-java-v4.md) .

### <a name="high-cpu-utilization"></a>Magas processzorkihasználtság
A leggyakoribb eset a processzor magas kihasználtsága. Az optimális késés érdekében a CPU-használatnak nagyjából 40%-nak kell lennie. A maximális (nem átlagos) CPU-kihasználtság figyeléséhez használja a 10 másodperces értéket. A CPU-tüskék gyakoribbak a több partíciós lekérdezések esetében, ahol több kapcsolat is lehetséges egyetlen lekérdezéshez.

#### <a name="solution"></a>Megoldás:
Az SDK-t használó ügyfélalkalmazás vertikálisan fel-vagy kibővíthető.

### <a name="connection-throttling"></a>Kapcsolatok szabályozása
A kapcsolatok szabályozása akkor fordulhat elő, ha a gazdagép vagy az Azure SNAT (PAT) portjának kimerülése vagy a csatlakozás korlátja.

### <a name="connection-limit-on-a-host-machine"></a>A gazdagépre vonatkozó kapcsolatonként
Bizonyos linuxos rendszerek (például a Red Hat) felső korláttal rendelkeznek a megnyitott fájlok teljes számánál. A Linux rendszerű szoftvercsatornák fájlokként vannak megvalósítva, így ez a szám a kapcsolatok teljes számát is korlátozza. Futtassa az alábbi parancsot.

```bash
ulimit -a
```

#### <a name="solution"></a>Megoldás:
A "nincs fájl" néven azonosított maximálisan engedélyezett megnyitott fájlok száma legalább 10 000-kor kell, hogy legyen. További információ: Azure Cosmos DB Java SDK v4 [teljesítményével kapcsolatos tippek](performance-tips-java-sdk-v4-sql.md).

### <a name="socket-or-port-availability-might-be-low"></a>Lehet, hogy a szoftvercsatorna vagy a port rendelkezésre állása alacsony
Az Azure-ban való futtatáskor a Java SDK-t használó ügyfelek elérhetik az Azure SNAT (PAT) portjának kimerülését.

#### <a name="solution-1"></a>1. megoldás:
Ha Azure-beli virtuális gépeken fut, kövesse a [SNAT-portok kimerülésének útmutatóját](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>2. megoldás:
Ha Azure App Serviceon fut, kövesse a [csatlakoztatási hibák hibaelhárítási útmutatóját](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) , és [használja a app Service diagnosztikát](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>3. megoldás:
Ha Azure Functionson fut, ellenőrizze, hogy az összes érintett szolgáltatáshoz (beleértve a Azure Cosmos DB) tartozó egyedi vagy statikus ügyfelek karbantartására vonatkozó [Azure functions javaslatot](../azure-functions/manage-connections.md#static-clients) követi-e. A [szolgáltatási korlátokat](../azure-functions/functions-scale.md#service-limits) a függvényalkalmazás üzemeltetésének típusa és mérete alapján vizsgálja meg.

#### <a name="solution-4"></a>4. megoldás:
Ha HTTP-proxyt használ, győződjön meg arról, hogy az képes támogatni az SDK-ban konfigurált kapcsolatok számát `GatewayConnectionConfig` . Ellenkező esetben a csatlakoztatási problémákkal szembesül.

### <a name="create-multiple-client-instances"></a>Több ügyfél-példány létrehozása
Több ügyfél-példány létrehozása a kapcsolati problémákhoz és időtúllépéshez vezethet.

#### <a name="solution-1"></a>1. megoldás:
Kövesse a [teljesítménnyel kapcsolatos tippeket](performance-tips-java-sdk-v4-sql.md#sdk-usage), és használjon egyetlen CosmosClient-példányt egy teljes alkalmazásban.

#### <a name="solution-2"></a>2. megoldás:
Ha az egyedi CosmosClient nem lehet egy alkalmazásban, javasoljuk, hogy a CosmosClient-ben ezen az API-n keresztül használja a több Cosmos-ügyfél közötti kapcsolatok megosztását `connectionSharingAcrossClientsEnabled(true)` . Ha a Cosmos-ügyfél több példánya is ugyanabban a JVM van, és több Cosmos-fiókkal is működik, ez lehetővé teszi a kapcsolat megosztását közvetlen módban, ha lehetséges a Cosmos-ügyfél példányai között. Vegye figyelembe, hogy ha ezt a beállítást választja, a rendszer az első példányú ügyfél kapcsolati konfigurációját (például a szoftvercsatorna időtúllépési konfigurációját, az Üresjárati időkorlát konfigurációját) fogja használni az összes többi ügyfél-példányhoz.

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
* A Azure Cosmos DB Java v4 SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-java-sdk-v4-sql.md) .
* A [Java v4](performance-tips-java-sdk-v4-sql.md)teljesítményére vonatkozó irányelvek ismertetése.
