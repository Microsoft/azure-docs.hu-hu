---
title: A többrégiós környezetekben elérhető Azure Cosmos SDK-k rendelkezésre állásának diagnosztizálása és megoldása
description: Ismerje meg az Azure Cosmos SDK rendelkezésre állási viselkedését többrégiós környezetben való működés esetén.
author: ealsur
ms.service: cosmos-db
ms.date: 02/16/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 641b7d44407f8f3760c673f45d69dcfdc8b363b8
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650983"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>A többrégiós környezetekben elérhető Azure Cosmos SDK-k rendelkezésre állásának diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

Ha **nem állít be előnyben részesített régiót**, az SDK-ügyfél alapértelmezett értéke az elsődleges régió:

|Fiók típusa |Olvasások |Írások |
|------------------------|--|--|
| Egyszeri írási régió | Elsődleges régió | Elsődleges régió |
| Több írási régió | Elsődleges régió  | Elsődleges régió  |

> [!NOTE]
> Az elsődleges régió az [Azure Cosmos-fiók régiójának](distribute-data-globally.md)első régiójára utal.
> Ha a területi beállításként megadott értékek nem egyeznek a meglévő Azure-régiókkal, a rendszer figyelmen kívül hagyja őket. Ha megegyeznek egy meglévő régióval, de a fiók nem replikálódik rá, akkor az ügyfél az elsődleges régióhoz tartozó következő előnyben részesített régióhoz fog csatlakozni.

> [!WARNING]
> Ha letiltja a végpont újbóli felderítését (amely hamis értékre van állítva), az ügyfél konfigurációja letiltja a jelen dokumentumban ismertetett összes feladatátvételi és rendelkezésre állási logikát.
> Ez a konfiguráció a következő paraméterekkel érhető el az egyes Azure Cosmos SDK-ban:
>
> * A [ConnectionPolicy. EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) tulajdonság a .NET v2 SDK-ban.
> * A [CosmosClientBuilder. endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) metódus a Java v4 SDK-ban.
> * A [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) paraméter a Python SDK-ban.
> * A [CosmosClientOptions. ConnectionPolicy. enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) paraméter a JS SDK-ban.

Normális körülmények között az SDK-ügyfél csatlakozni fog az előnyben részesített régióhoz (ha be van állítva egy területi beállítás) vagy az elsődleges régióra (ha nincs beállítva preferencia), és a műveletek az adott régióra korlátozódnak, kivéve, ha az alábbi esetek bármelyike bekövetkezik.

Ezekben az esetekben az Azure Cosmos SDK-t használó ügyfél elérhetővé teszi a naplókat, és az újrapróbálkozási információkat a **művelet diagnosztikai információinak** részeként tartalmazza:

* A válaszok *RequestDiagnosticsString* tulajdonsága a .NET v2 SDK-ban.
* A .NET v3 SDK-beli válaszok és kivételek *diagnosztikai* tulajdonsága.
* A *getDiagnostics()* metódussal a Java v4 SDK-beli válaszoknál és kivételeknél.

A következő régió beállításakor az SDK-ügyfél a fiók régiójának listáját fogja használni, rangsorolva az előnyben részesített régiókat (ha vannak ilyenek).

Az SLA-garanciákkal kapcsolatos átfogó Részletes információkért tekintse meg az SLA-kat a [rendelkezésre álláshoz](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Régió eltávolítása a fiókból

Ha eltávolít egy régiót egy Azure Cosmos-fiókból, a fiókot aktívan használó SDK-ügyfelek a háttérbeli válasz kódján keresztül fogják észlelni a régió eltávolítását. Az ügyfél ezután nem elérhetőként jelöli meg a regionális végpontot. Az ügyfél újrapróbálkozik az aktuális művelettel, és az összes jövőbeli művelet véglegesen át lesz irányítva a következő régióba, a preferencia sorrendben. Abban az esetben, ha a preferencia-lista csak egy bejegyzést tartalmaz (vagy üres volt), de a fióknak más régiói is vannak, akkor a fiók lista következő régiójára irányítja át.

## <a name="adding-a-region-to-an-account"></a>Régió hozzáadása egy fiókhoz

Az Azure Cosmos SDK-ügyfél 5 percenként beolvassa a fiók konfigurációját, és frissíti azokat a régiókat, amelyekről tisztában van.

Ha eltávolít egy régiót, majd később visszaadja azt a fiókhoz, ha a hozzáadott régió magasabb prioritási sorrendet tartalmaz az SDK konfigurációjában, mint az aktuálisan csatlakoztatott régió, az SDK visszavált a régió végleges használatára. A hozzáadott régió észlelése után a rendszer az összes jövőbeli kérelmet átirányítja.

Ha úgy konfigurálja az ügyfelet, hogy egy olyan régióhoz kapcsolódjon, amelyhez az Azure Cosmos-fiók nem rendelkezik, a rendszer figyelmen kívül hagyja az előnyben részesített régiót. Ha később hozzáadja ezt a régiót, akkor az ügyfél észleli azt, és véglegesen átvált az adott régióra.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Az írási régió feladatátvétele egyetlen írási régióbeli fiókban

Ha az aktuális írási régió feladatátvételét kezdeményezi, a következő írási kérelem sikertelen lesz, amely egy ismert háttérbeli választ ad. Ha a rendszer ezt a választ észleli, az ügyfél lekérdezi a fiókot az új írási régió megismeréséhez, és folytatja az aktuális művelet ismételt megadását és az összes jövőbeli írási művelet végleges átirányítását az új régióba.

## <a name="regional-outage"></a>Regionális leállás

Ha a fiók egyetlen írási régió, és a regionális leállás egy írási művelet során következik be, a viselkedés hasonló a [manuális feladatátvételhez](#manual-failover-single-region). Olvasási kérelmek vagy több írási régió fiókjai esetén a viselkedés hasonló a [régió eltávolításához](#remove-region).

## <a name="session-consistency-guarantees"></a>Munkamenet konzisztenciájának garanciái

A [munkamenet konzisztenciájának](consistency-levels.md#guarantees-associated-with-consistency-levels)használatakor az ügyfélnek biztosítania kell, hogy a saját írásait tudja olvasni. Az olyan egyszeri írási régió fiókjaiban, amelyekben az olvasási régió beállításai eltérnek az írási régiótól, előfordulhat, hogy a felhasználó írást okoz, és a helyi régióból való olvasáskor a helyi régió még nem kapta meg az adatreplikálást (a könnyű korlátozás sebessége). Ilyen esetekben az SDK észleli az olvasási művelet adott hibáját, és újrapróbálkozik az olvasással az elsődleges régióban a munkamenet konzisztenciájának biztosítása érdekében.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Átmeneti kapcsolódási problémák a TCP protokollon

Olyan esetekben, amikor az Azure Cosmos SDK-ügyfél a TCP protokoll használatára van konfigurálva, egy adott kérelem esetében előfordulhatnak olyan helyzetek, amikor a hálózati feltételek átmenetileg érintik egy adott végponttal folytatott kommunikációt. Ezek az ideiglenes hálózati feltételek a TCP-időtúllépések és a szolgáltatás nem érhető el (HTTP 503) hibákat okozhatnak. Az ügyfél néhány másodpercen belül újra megkísérli a kérést helyileg ugyanazon a végponton, mielőtt felszínre kerül a hiba.

Ha a felhasználó több régióval konfigurált egy előnyben részesített régiót, és az Azure Cosmos-fiók több írási régió vagy egyetlen írási régió, és a művelet egy olvasási kérelem, az ügyfél észlelni fogja a helyi hibát, majd próbálja megismételni az egyetlen műveletet a következő régióban a preferencia listából.

## <a name="next-steps"></a>Következő lépések

* Tekintse át a [rendelkezésre állási SLA](high-availability.md#slas-for-availability)-kat.
* A legújabb [.net SDK](sql-api-sdk-dotnet-standard.md) használata
* A legújabb [Java SDK](sql-api-sdk-java-v4.md) használata
* A legújabb [PYTHON SDK](sql-api-sdk-python.md) használata
* A legújabb [Node SDK](sql-api-sdk-node.md) használata
