---
title: A többrégiós környezetekben elérhető Azure Cosmos SDK-k rendelkezésre állásának diagnosztizálása és megoldása
description: Ismerje meg az Azure Cosmos SDK rendelkezésre állási viselkedését többrégiós környezetben való működés esetén.
author: ealsur
ms.service: cosmos-db
ms.date: 10/05/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 400795d20b6e7ad919f5cbbfa6078987bb65297e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743964"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>A többrégiós környezetekben elérhető Azure Cosmos SDK-k rendelkezésre állásának diagnosztizálása és megoldása

Ez a cikk ismerteti az Azure Cosmos SDK-k legújabb verziójának viselkedését, ha egy adott régióhoz vagy egy régió feladatátvételéhez kapcsolódási probléma jelenik meg.

Az összes Azure Cosmos SDK lehetőséget biztosít a területi beállítások testreszabására. A különböző SDK-k a következő tulajdonságokat használják:

* A [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) tulajdonság a .NET v2 SDK-ban.
* A [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) vagy a [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) tulajdonság a .net v3 SDK-ban.
* A [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) metódus a Java v4 SDK-ban.
* A [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) paraméter a Python SDK-ban.
* A [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) paraméter a JS SDK-ban.

A területi beállítások megadásakor az ügyfél a következő táblázatban említettek szerint csatlakozik a régióhoz:

|Fiók típusa |Olvasások |Írások |
|------------------------|--|--|
| Egyszeri írási régió | Előnyben részesített régió | Elsődleges régió  |
| Több írási régió | Előnyben részesített régió | Előnyben részesített régió  |

Ha nem állít be előnyben részesített régiót:

|Fiók típusa |Olvasások |Írások |
|------------------------|--|--|
| Egyszeri írási régió | Elsődleges régió | Elsődleges régió |
| Több írási régió | Elsődleges régió  | Elsődleges régió  |

> [!NOTE]
> Az elsődleges régió az [Azure Cosmos-fiók régiójának](distribute-data-globally.md) első régiójára vonatkozik

Ha az alábbi esetek bármelyike előfordul, az Azure Cosmos SDK-t használó ügyfél megjeleníti a naplókat, és az újrapróbálkozási információkat a **művelet diagnosztikai információinak**részeként tartalmazza:

* A válaszok *RequestDiagnosticsString* tulajdonsága a .NET v2 SDK-ban.
* A .NET v3 SDK-beli válaszok és kivételek *diagnosztikai* tulajdonsága.
* A *getDiagnostics ()* metódus a válaszokat és kivételeket a Java v4 SDK-ban.

A következő régió beállításakor az SDK-ügyfél a fiók régiójának listáját fogja használni, rangsorolva az előnyben részesített régiókat (ha vannak ilyenek).

Az SLA-garanciákkal kapcsolatos átfogó Részletes információkért tekintse meg az SLA-kat a [rendelkezésre álláshoz](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Régió eltávolítása a fiókból

Ha eltávolít egy régiót egy Azure Cosmos-fiókból, a fiókot aktívan használó SDK-ügyfelek a háttérbeli válasz kódján keresztül fogják észlelni a régió eltávolítását. Az ügyfél ezután nem elérhetőként jelöli meg a regionális végpontot. Az ügyfél újrapróbálkozik az aktuális művelettel, és az összes jövőbeli művelet véglegesen át lesz irányítva a következő régióba, a preferencia sorrendben.

## <a name="adding-a-region-to-an-account"></a>Régió hozzáadása egy fiókhoz

Az Azure Cosmos SDK-ügyfél 5 percenként beolvassa a fiók konfigurációját, és frissíti azokat a régiókat, amelyekről tisztában van.

Ha eltávolít egy régiót, majd később visszaadja azt a fiókhoz, ha a hozzáadott régió magasabb prioritási sorrendet tartalmaz az SDK konfigurációjában, mint az aktuálisan csatlakoztatott régió, az SDK visszavált a régió végleges használatára. A hozzáadott régió észlelése után a rendszer az összes jövőbeli kérelmet átirányítja.

Ha úgy konfigurálja az ügyfelet, hogy egy olyan régióhoz kapcsolódjon, amelyhez az Azure Cosmos-fiók nem rendelkezik, a rendszer figyelmen kívül hagyja az előnyben részesített régiót. Ha később hozzáadja ezt a régiót, akkor az ügyfél észleli azt, és véglegesen átvált az adott régióra.

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Az írási régió feladatátvétele egyetlen írási régióbeli fiókban

Ha az aktuális írási régió feladatátvételét kezdeményezi, a következő írási kérelem sikertelen lesz, amely egy ismert háttérbeli választ ad. Ha a rendszer ezt a választ észleli, az ügyfél lekérdezi a fiókot az új írási régió megismeréséhez, és folytatja az aktuális művelet ismételt megadását és az összes jövőbeli írási művelet végleges átirányítását az új régióba.

## <a name="regional-outage"></a>Regionális leállás

Ha a fiók egyetlen írási régió, és a regionális leállás egy írási művelet során következik be, a viselkedés hasonló a [manuális feladatátvételhez](#manual-failover-single-region). Olvasási kérelmek vagy több írási régió fiókjai esetén a viselkedés hasonló a [régió eltávolításához](#remove-region).

## <a name="session-consistency-guarantees"></a>Munkamenet konzisztenciájának garanciái

A [munkamenet konzisztenciájának](consistency-levels.md#guarantees-associated-with-consistency-levels)használatakor az ügyfélnek biztosítania kell, hogy a saját írásait tudja olvasni. Az olyan egyszeri írási régió fiókjaiban, amelyekben az olvasási régió beállításai eltérnek az írási régiótól, előfordulhat, hogy a felhasználó írást okoz, és a helyi régióból való olvasáskor a helyi régió még nem kapta meg az adatreplikálást (a könnyű korlátozás sebessége). Ilyen esetekben az SDK észleli az olvasási művelet adott hibáját, és újrapróbálkozik a központi régió olvasásával a munkamenet konzisztenciájának biztosítása érdekében.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Átmeneti kapcsolódási problémák a TCP protokollon

Olyan esetekben, amikor az Azure Cosmos SDK-ügyfél a TCP protokoll használatára van konfigurálva, egy adott kérelem esetében előfordulhatnak olyan helyzetek, amikor a hálózati feltételek átmenetileg érintik egy adott végponttal folytatott kommunikációt. Ezek az ideiglenes hálózati feltételek TCP-időtúllépésként használhatók. Az ügyfél néhány másodpercen belül újra megkísérli a kérést helyileg ugyanazon a végponton.

Ha a felhasználó több régióval konfigurált egy előnyben részesített régiót, és az Azure Cosmos-fiók több írási régió vagy egyetlen írási régió, és a művelet egy olvasási kérelem, az ügyfél újra megpróbálja ezt az egyetlen műveletet a következő régióban a preferencia listából.

## <a name="next-steps"></a>Következő lépések

* Tekintse át a [rendelkezésre állási SLA](high-availability.md#slas-for-availability)-kat.
* A legújabb [.net SDK](sql-api-sdk-dotnet-standard.md) használata
* A legújabb [Java SDK](sql-api-sdk-java-v4.md) használata
* A legújabb [PYTHON SDK](sql-api-sdk-python.md) használata
* A legújabb [Node SDK](sql-api-sdk-node.md) használata
