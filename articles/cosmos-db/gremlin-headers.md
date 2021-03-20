---
title: Azure Cosmos DB Gremlin-válasz fejlécei
description: A további hibaelhárítást lehetővé tevő kiszolgálói válasz metaadatainak dokumentációja
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600520"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin-kiszolgáló válaszának fejlécei
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Ez a cikk azokat a fejléceket ismerteti, amelyeket a Cosmos DB Gremlin-kiszolgáló visszaküld a hívónak a kérelmek végrehajtásakor. Ezek a fejlécek segítenek a kérelmek teljesítményének hibaelhárításában, a Cosmos DB szolgáltatással natív módon integrálható alkalmazások létrehozásában és az ügyfélszolgálat egyszerűsítésében.

Ne feledje, hogy ezen fejlécek függőségének figyelembevételével korlátozza az alkalmazás hordozhatóságát más Gremlin-megvalósításokra. Cserébe egyre szorosabb integráció Cosmos DB Gremlin. Ezek a fejlécek nem TinkerPop standardok.

## <a name="headers"></a>Fejlécek

| Fejléc | Típus | Minta értéke | Ha foglalt | Magyarázat |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11,3243 | Sikeres és sikertelen művelet-végrehajtási trendek | A [kérelem egységében (ru/s vagy RUs)](request-units.md) felhasznált gyűjtemények vagy adatbázis-átviteli sebességek mennyisége részleges válaszüzenet esetén. Ez a fejléc a több adattömbökkel rendelkező kérelmek minden folytatásában megtalálható. Egy adott válasz adathalmazának díját tükrözi. Csak olyan kérelmek esetében, amelyek egyetlen válaszból állnak, ez a fejléc a bejárás teljes költsége. Azonban az összetett bejárásokat többsége esetében ez az érték részleges költségeket jelent. |
| **x-ms-total-request-charge** | double | 423,987 | Sikeres és sikertelen művelet-végrehajtási trendek | A kérések egységében felhasznált gyűjtési vagy adatbázis-átviteli sebesség [(ru/s vagy RUs)](request-units.md) a teljes kérelemhez. Ez a fejléc a több adattömbökkel rendelkező kérelmek minden folytatásában megtalálható. A kérelem kezdete óta halmozott díjat jelez. Ennek a fejlécnek az értéke az utolsó adathalmazban a kérelem teljes díját jelzi. |
| **x-MS-Server-Time-MS** | double | 13,75 | Sikeres és sikertelen művelet-végrehajtási trendek | Ez a fejléc a késéssel kapcsolatos hibaelhárítási célokhoz tartozik. Megadja azt az időtartamot ezredmásodpercben, ameddig Cosmos DB Gremlin-kiszolgálónak végre kellett hajtania a részleges válaszüzenetet. A fejléc értékének használata és a kérelmek összesített késése alkalmazásokkal való összehasonlítása kiszámíthatja a hálózati késések terhelését. |
| **x-MS-Total-Server-Time-MS** | double | 130,512 | Sikeres és sikertelen művelet-végrehajtási trendek | Az Cosmos DB Gremlin-kiszolgáló teljes bejárási ideje összesen ezredmásodpercben. Ez a fejléc minden részleges választ tartalmaz. A kérelem kezdete óta halmozott végrehajtási időt jelképezi. Az utolsó válasz a végrehajtás teljes idejét jelzi. Ez a fejléc hasznos lehet az ügyfél és a kiszolgáló közötti különbségtételhez a késés forrásaként. Összehasonlíthatja a bejárás végrehajtási idejét az ügyfélen a fejléc értékével. |
| **x-ms-status-code** | hosszú | 200 | Sikeres és sikertelen művelet-végrehajtási trendek | A fejléc a kérelem befejezésének vagy megszüntetésének belső okát jelzi. Az alkalmazásnak javasoljuk, hogy tekintse meg a fejléc értékét, és végezze el a megfelelő lépéseket. |
| **x-MS-alállapot-kód** | hosszú | 1003 | Csak hiba | Az Cosmos DB egy többmodelles adatbázis, amely egységes tárolási rétegre épül. Ez a fejléc további információkat tartalmaz a hibák okairól, ha a magas rendelkezésre állású verem alsóbb rétegeiben hiba történik. Az alkalmazás a fejléc tárolásához és a Cosmos DB ügyfélszolgálathoz való kapcsolódáskor javasolt. A fejléc értéke hasznos Cosmos DB mérnök számára a gyors hibaelhárításhoz. |
| **x-ms-retry-after-ms** | karakterlánc (TimeSpan) | "00:00:03.9500000" | Csak hiba | Ez a fejléc egy .NET- [TimeSpan](/dotnet/api/system.timespan) típusának karakterláncos ábrázolása. Ez az érték csak olyan kérésekben szerepel, amelyeken a kiépített átviteli sebesség kimerítése miatt sikertelen volt. Az alkalmazásnak újra el kell küldenie a bejárást a megadott időtartam elteltével. |
| **x-ms-activity-id** | karakterlánc (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Sikeres és sikertelen művelet-végrehajtási trendek | A fejléc a kérelmek egyedi, kiszolgálóoldali azonosítóját tartalmazza. Minden kéréshez a kiszolgáló egyedi azonosítót rendel hozzá követési célokra. Az alkalmazásoknak a kiszolgáló által visszaadott tevékenység-azonosítókat kell naplóznia azon kérések esetén, amelyekre az ügyfélnek szüksége lehet az ügyfélszolgálattal kapcsolatos kapcsolatfelvételhez. Cosmos DB támogatási személyzet a Cosmos DB Service telemetria ezen azonosítói alapján adott kérelmeket talál. |

## <a name="status-codes"></a>Állapotkódok

`x-ms-status-code`Az állapot attribútumhoz a kiszolgáló által visszaadott leggyakoribb kódokat az alábbi lista tartalmazza.

| Állapot | Magyarázat |
| --- | --- |
| **401** | Hibaüzenet jelenik meg `"Unauthorized: Invalid credentials provided"` , ha a hitelesítési jelszó nem egyezik Cosmos db a fiók kulcsával. Navigáljon a Azure Portal Cosmos DB Gremlin-fiókjához, és győződjön meg arról, hogy a kulcs helyes.|
| **404** | Egyidejű műveletek, amelyek egyazon Edge vagy csúcspont törlését és frissítését kísérli meg egyszerre. Az `"Owner resource does not exist"` (Tulajdonos-erőforrás nem létezik) hibaüzenet azt jelzi, hogy a kapcsolati paraméterekben `/dbs/<database name>/colls/<collection or graph name>` formátumban megadott adatbázis vagy gyűjtemény helytelen.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Ez általában akkor fordul elő, ha a gráfban már létezik egy adott azonosítójú csúcspont vagy él.| 
| **412** | Az állapotkód a következő hibaüzenettel egészül ki: `"PreconditionFailedException": One of the specified pre-condition is not met` . Ez a hiba azt jelzi, hogy egy optimista egyidejűségi szabályozás sérti az Edge vagy a csúcspont beolvasása és az áruházba való visszaírása között a módosítás után. A hiba előfordulásának leggyakoribb esetei a tulajdonságok módosítása, például: `g.V('identifier').property('name','value')` . A Gremlin motor beolvassa a csúcspontot, módosítja, és visszaírja azt. Ha egy másik, párhuzamosan futó bejárási kísérlet ugyanazt a csúcspontot vagy szegélyt próbálja meg írni, akkor az egyik ilyen hibaüzenetet kap. Az alkalmazásnak újra be kell küldenie a kiszolgálóra a bejárást.| 
| **429** | A kérelem szabályozása megtörtént, és a rendszer az **x-MS-újrapróbálkozás** után újrapróbálkozik a-MS értékkel| 
| **500** | Ha egy hibaüzenetben megtalálható a(z) `"NotFoundException: Entity with the specified id does not exist in the system."`, az azt jelzi, hogy egy adatbázis és/vagy gyűjtemény újra létre lett hozva ugyanazzal a névvel. Ez a hiba 5 percen belül eltűnik, mivel a változás propagálja és érvényteleníti a különböző Cosmos DB-összetevők gyorsítótárait. A hiba elkerüléséhez minden alkalommal egyedi adatbázis- és gyűjteményneveket használjon.| 
| **1000** | A rendszer ezt az állapotkódot adja vissza, ha a kiszolgáló sikeresen elemezte az üzenetet, de nem sikerült végrehajtani. Általában a lekérdezéssel kapcsolatos problémát jelez.| 
| **1001** | Ezt a kódot akkor adja vissza a rendszer, ha a kiszolgáló elvégzi a bejárási végrehajtást, de a válasz nem szerializálható vissza az ügyfélre. Ez a hiba akkor fordulhat elő, ha a bejárás összetett eredményt hoz létre, amely túl nagy, vagy nem felel meg a TinkerPop protokoll specifikációjának. Az alkalmazásnak le kell egyszerűsíteni a bejárást, amikor a hibát tapasztalja. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` Ha a bejárás meghaladja az engedélyezett memória korlátját, a rendszer visszaadja. A memória korlátja **2 GB** /bejárási érték.| 
| **1004** | Ez az állapotkód helytelenül formázott gráf-kérelmet jelez. A kérelem helytelenül formázható, ha a deszerializálás sikertelen, a nem érték típusú típus deszerializálása érték típusú vagy nem támogatott Gremlin művelet. Az alkalmazás nem próbálkozhat újra a kéréssel, mert nem lesz sikeres. | 
| **1007** | Ezt az állapotkódot általában a hibaüzenet adja vissza `"Could not process request. Underlying connection has been closed."` . Ez a helyzet akkor fordulhat elő, ha az ügyfél illesztőprogramja a kiszolgáló által lezárt kapcsolatok használatát kísérli meg. Az alkalmazásnak újra kell próbálkoznia a bejárással egy másik kapcsolatban.
| **1008** | Cosmos DB Gremlin-kiszolgáló megszakíthatja a kapcsolatokat a fürt forgalmának újraelosztása érdekében. Az ügyfél-illesztőprogramoknak ezt a helyzetet kell használniuk, és csak élő kapcsolatok használatával küldhetnek kérelmeket a kiszolgálónak. Esetenként előfordulhat, hogy az ügyfél-illesztőprogramok nem észlelik, hogy a kapcsolatok bezárultak. Ha az alkalmazás hibát észlel, `"Connection is too busy. Please retry after sometime or open more connections."` akkor újra kell próbálkoznia egy másik kapcsolatban való bejárással.
| **1009** | A művelet nem fejeződött be a megadott időn belül, és a kiszolgáló megszakította a műveletet. Optimalizálja a bejárásokat, hogy gyorsan fusson a csúcsok vagy élek a keskeny keresési hatókörbe való bejáráskor. A kérelem időtúllépésének alapértelmezett értéke **60 másodperc**. |

## <a name="samples"></a>Példák

Egy Gremlin.Net alapuló minta ügyfélalkalmazás, amely egy status attribútumot olvas:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Egy példa, amely bemutatja, hogyan olvashatja el az állapot attribútumot a Gremlin Java-ügyfélből:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Következő lépések
* [Azure Cosmos DB HTTP-állapotkódok](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Gyakori Azure Cosmos DB REST-válaszok fejlécei](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [A TinkerPop Graph-illesztőprogram szolgáltatójának követelményei]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
