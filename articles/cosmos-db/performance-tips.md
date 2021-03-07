---
title: A .NET SDK v2 Azure Cosmos DB teljesítményével kapcsolatos tippek
description: Az ügyfél konfigurációs beállításainak megismerése Azure Cosmos DB .NET v2 SDK teljesítményének javításához.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 57b3d5853f83fc7ee75538d7966f5e20b1a64cd6
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428949"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Teljesítménnyel kapcsolatos tippek az Azure Cosmos DB Java SDK v2-höz
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás. További információkért lásd: [a tárolók teljesítményének kiépítése](how-to-provision-container-throughput.md) vagy [az adatbázis átviteli sebességének kiépítése](how-to-provision-database-throughput.md). Mivel azonban a Azure Cosmos DB hálózati hívásokon keresztül érhetők el, ügyféloldali optimalizálások érhetők el, amelyekkel elérheti a maximális teljesítményt az [SQL .net SDK](sql-api-sdk-dotnet-standard.md)használatakor.

Tehát ha javítani kívánja az adatbázis teljesítményét, vegye figyelembe a következő lehetőségeket:

## <a name="upgrade-to-the-net-v3-sdk"></a>Frissítés a .NET v3 SDK-ra

Megjelent a [.net v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) . Ha a .NET v3 SDK-t használja, tekintse meg a következő információkat a [.net v3 teljesítmény-útmutatójában](performance-tips-dotnet-sdk-v3-sql.md) :

- Alapértelmezett értéke a közvetlen TCP-üzemmód
- Stream API-támogatás
- Egyéni szerializáló támogatása a System.Text.JShasználatának engedélyezéséhez
- Integrált batch-és tömeges támogatás

## <a name="hosting-recommendations"></a>Üzemeltetési javaslatok

**A lekérdezés-igényes számítási feladatokhoz a Linux vagy a Windows 32 bites gazdagép-feldolgozás helyett a Windows 64 bites használata**

A jobb teljesítmény érdekében javasoljuk a Windows 64 bites gazdagépek feldolgozását. Az SQL SDK tartalmaz egy natív ServiceInterop.dll a lekérdezések helyi elemzéséhez és optimalizálásához. A ServiceInterop.dll csak a Windows x64 platformon támogatott. Linux és egyéb nem támogatott platformok esetén, ahol a ServiceInterop.dll nem érhető el, az átjáróra további hálózati hívás történik az optimalizált lekérdezés beszerzéséhez. A következő típusú alkalmazások alapértelmezés szerint 32 bites gazdagép-feldolgozást használnak. Ha módosítani szeretné a gazdagép feldolgozását 64 bites feldolgozásra, kövesse az alábbi lépéseket az alkalmazás típusa alapján:

- A végrehajtható alkalmazások esetében úgy módosíthatja a gazdagépek feldolgozását, hogy a [platform célját](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) **x64**  értékre állítja a **Projekt tulajdonságai** ablakban a **Build** lapon.

- A VSTest-alapú tesztelési projektek esetében   >    >  a Visual Studio **test** menüben válassza a tesztelési beállítások **alapértelmezett processzor-architektúra x64-ként** lehetőséget.

- Helyileg telepített ASP.NET-webalkalmazások esetében a gazdagépek feldolgozását úgy módosíthatja, hogy kiválasztja **a IIS Express 64 bites verzióját a webhelyekhez és projektekhez** a **Tools**  >  **Options**  >  **Projects és a Solutions**  >  **webes projektjei** alatt.

- Az Azure-on üzembe helyezett webalkalmazások esetében a **ASP.net a** **64 bites** platform kiválasztásával módosíthatja a Azure Portal.

> [!NOTE] 
> Alapértelmezés szerint az új Visual Studio-projektek **bármely CPU**-ra vannak beállítva. Javasoljuk, hogy állítsa a projektet **x64** -re, hogy ne váltson **x86**-ra. **Bármely CPU** -ra beállított projekt könnyen válthat **x86** -ra, ha csak x86-os függőség van hozzáadva.<br/>
> ServiceInterop.dllnak abban a mappában kell lennie, amelyre az SDK DLL-t végrehajtja. Ez csak akkor fontos, ha manuálisan másol DLL-eket, vagy egyéni Build-vagy üzembe helyezési rendszert használ.
    
**Kiszolgálóoldali Garbage-gyűjtemény bekapcsolása (GC)**

Bizonyos esetekben a Garbage-gyűjtemények gyakoriságának csökkentése is segíthet. A .NET-ben állítsa be a [gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) a következőre: `true` .

**Az ügyfél számítási felskálázása**

Ha magas átviteli sebességű (több mint 50 000 RU/s) tesztelést végez, az ügyfélalkalmazás a processzor vagy a hálózat kihasználtságának korlátozásával járó szűk keresztmetszet miatt válhat. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

> [!NOTE] 
> A magas CPU-használat nagyobb késést és kérelem időtúllépési kivételt okozhat.

## <a name="networking"></a><a id="networking"></a> Hálózati

**Csatlakoztatási házirend: közvetlen kapcsolási mód használata**

A .NET v2 SDK alapértelmezett csatlakoztatási módja az átjáró. A (z `DocumentClient` ) paraméter használatával konfigurálja a csatlakozás módját a példány építése során `ConnectionPolicy` . Közvetlen mód használata esetén a paramétert is be kell állítania `Protocol` `ConnectionPolicy` . Ha többet szeretne megtudni a különböző csatlakozási lehetőségekről, tekintse meg a [kapcsolódási módokat](sql-sdk-connection-modes.md) ismertető cikket.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**Rövid élettartamú portok elfogyása**

Ha a példányok nagy mennyiségű vagy magas portszámú használatot lát, először ellenőrizze, hogy az ügyfél példányai egyediek-e. Más szóval az ügyfél példányának egyedinek kell lennie az alkalmazás élettartama szempontjából.

Ha a TCP protokollon fut, az ügyfél a hosszú élettartamú kapcsolatok és a HTTPS protokoll használata esetén is optimalizálja a késést, amely 2 perc inaktivitás után leállítja a kapcsolatokat.

Olyan helyzetekben, ahol ritka hozzáférése van, és ha az átjáró mód eléréséhez képest nagyobb számú kapcsolatra van szüksége, a következőket teheti:

* Konfigurálja a [ConnectionPolicy. PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) tulajdonságot a `PrivatePortPool` következőre: (a keretrendszer verziószáma>= 4.6.1 és a .net Core verziója >= 2,0): Ez a tulajdonság lehetővé teszi, hogy az SDK a különböző Azure Cosmos db végpontokhoz tartozó ideiglenes portok kis készletét használja.
* A [ConnectionPolicy. IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) tulajdonság konfigurálásának 10 percnél nagyobbnak vagy azzal egyenlőnek kell lennie. Az ajánlott értékek 20 perc és 24 óra között vannak.

**OpenAsync meghívása az első kérés indítási késésének elkerülése érdekében**

Alapértelmezés szerint az első kérelem nagyobb késéssel jár, mert be kell olvasnia a címek útválasztási táblázatát. Ha az [SDK v2](sql-api-sdk-dotnet.md)-t használja, az `OpenAsync()` inicializálás során egyszer hívja meg az indítási késést az első kérelemnél. A hívás így néz ki: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` a a fiókban lévő összes tárolóhoz tartozó címek útválasztási táblázatának beszerzésére vonatkozó kérelmeket hoz létre. A sok tárolóval rendelkező, de az alkalmazáshoz hozzáférő fiókok esetében `OpenAsync` szükségtelen mennyiségű adatforgalmat eredményezne, ami lelassítja az inicializálást. Így előfordulhat, hogy a használata `OpenAsync` nem hasznos ebben a forgatókönyvben, mert lelassítja az alkalmazás indítását.

**A teljesítmény érdekében az azonos Azure-régióban lévő ügyfelek rézvezetékes végezhet**

Ha lehetséges, helyezzen olyan alkalmazásokat, amelyek a Azure Cosmos DB-adatbázissal megegyező régióban Azure Cosmos DB hívnak. Íme egy hozzávetőleges összehasonlítás: az azonos régión belüli Azure Cosmos DB meghívása 1 MS és 2 MS között fejeződik be, de az USA nyugati és keleti partja közötti késés meghaladja a 50 MS-ot. Ez a késés a kérelemtől függően változhat, attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure-adatközpont határán. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="A Azure Cosmos DB-kapcsolatok házirendje" border="false":::

**A szálak/feladatok számának növelésével**
<a id="increase-threads"></a>

Mivel a hálózatra irányuló Azure Cosmos DB hívások a hálózaton keresztül történnek, lehetséges, hogy módosítania kell a kérések párhuzamossági fokát, hogy az ügyfélalkalmazás a kérelmek közötti minimális várakozási időt töltsön. Ha például a .NET- [feladatok párhuzamos könyvtárát](/dotnet/standard/parallel-programming/task-parallel-library-tpl)használja, akkor hozzon létre több száz feladatot a Azure Cosmos db olvasására vagy írására.

**Gyorsított hálózatkezelés engedélyezése**
 
A késés és a CPU vibrálás csökkentése érdekében javasoljuk, hogy engedélyezze a gyorsított hálózatkezelést az ügyfél virtuális gépei között. Lásd: [Windows rendszerű virtuális gép gyorsított hálózatkezeléssel való létrehozása](../virtual-network/create-vm-accelerated-networking-powershell.md) vagy [linuxos virtuális gép létrehozása gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-használat

**A legújabb SDK telepítése**

A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg a [Azure Cosmos db SDK](sql-api-sdk-dotnet-standard.md) -lapokat a legújabb SDK-val, és tekintse át a módosításokat.

**Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

`DocumentClient`Az egyes példányok a Thread-Safe és a hatékony kapcsolatok kezelése és a címek gyorsítótárazása közvetlen üzemmódban való működés esetén. A hatékony és az SDK-ügyfél teljesítményének növelése érdekében ajánlott egyetlen példányt használni az `AppDomain` alkalmazás élettartamára.

**System.Net-MaxConnections növelésének engedélyezése gazdagépen az átjáró mód használatakor**

Azure Cosmos DB kérések HTTPS/REST protokollon keresztül történnek, amikor átjáró üzemmódot használ. A rendszer az alapértelmezett kapcsolódási korlátot az állomásnév vagy az IP-cím alapján végzi. Előfordulhat, hogy `MaxConnections` magasabb értékre kell állítani (100 – 1 000), hogy az ügyféloldali kódtár több egyidejű kapcsolatot is használhat Azure Cosmos DBhoz. A .NET SDK 1.8.0 és újabb verzióiban a [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) alapértelmezett értéke 50. Az érték módosításához beállíthatja a [Documents. Client. ConnectionPolicy. MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) értéket magasabb értékre.

**A particionált gyűjtemények párhuzamos lekérdezésének hangolása**

Az SQL .NET SDK 1.9.0 és újabb verziói támogatják a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemények párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy jobb lekérdezési késést és adatátviteli sebességet biztosítanak, mint a soros A párhuzamos lekérdezések két paramétert biztosítanak, amelyeket az igényeinek megfelelően hangolhat: 
- `MaxDegreeOfParallelism` a párhuzamosan lekérdezhető partíciók maximális számát szabályozza. 
- `MaxBufferedItemCount` az előre lehívott eredmények számát szabályozza.

***A párhuzamossági fok finomhangolása***

A párhuzamos lekérdezés több partíció párhuzamos lekérdezésével működik. Az egyes partíciók adatait azonban a lekérdezésre vonatkozó sorosan kell beolvasni. Az `MaxDegreeOfParallelism` [SDK v2](sql-api-sdk-dotnet.md) és a partíciók számának beállítása a legjobb lehetőség a legtöbb teljesítményű lekérdezés megvalósítására, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, megadhatja a párhuzamosság mértékét magas számra. A rendszer kijelöli a minimális (partíciók számát, a felhasználó által megadott bemenetet) a párhuzamosság foka alapján.

A párhuzamos lekérdezések a legnagyobb haszonnal járnak, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény particionálva van, és a lekérdezés által visszaadott összes adat egy része néhány partícióra koncentrál (az egyik partíció a legrosszabb ESET), akkor ezek a partíciók a lekérdezés teljesítményét szűk keresztmetszetbe helyezik.

***MaxBufferedItemCount finomhangolása***
    
A párhuzamos lekérdezés úgy lett kialakítva, hogy előzetesen beolvassa az eredményeket, miközben az ügyfél az aktuális eredményt dolgozza fel. Ez az előzetes lehívás a lekérdezés teljes késésének javítására nyújt segítséget. A `MaxBufferedItemCount` paraméter korlátozza az előre lehívott eredmények számát. Állítsa be a `MaxBufferedItemCount` visszaadott eredmények várt számát (vagy egy magasabb számot), hogy a lekérdezés a lehető legtöbbet fogadja az előzetes lekéréstől.

Az előzetes lekérés ugyanúgy működik, függetlenül a párhuzamosság mértékétől, és egyetlen puffer van az összes partícióból származó adatokhoz.  

**Leállítási megvalósítása RetryAfter időközönként**

A teljesítmény tesztelése során növelje a terhelést, amíg a rendszer kis mennyiségű kérést nem szabályoz. Ha a kérelmek szabályozása megtörténik, az ügyfélalkalmazás a kiszolgáló által megadott újrapróbálkozási időközre vonatkozó biztonsági mentést hajt végre a szabályozásban. A leállítási tiszteletben tartásával biztosíthatja, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia. 

Az újrapróbálkozási szabályzat támogatása a következő SDK-k részét képezi:
- Az [SQL-hez készült .net SDK](sql-api-sdk-dotnet.md) és az [SQL Java SDK](sql-api-sdk-java.md) -hoz tartozó 1.8.0 és újabb verzió
- A [Node.js SDK for SQL](sql-api-sdk-node.md) és a [PYTHON SDK for SQL](sql-api-sdk-python.md) 1.9.0 verziója
- A [.net Core](sql-api-sdk-dotnet-core.md) SDK-k összes támogatott verziója 

További információ: [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter).
    
A .NET SDK 1,19-es és újabb verzióiban van egy mechanizmus a további diagnosztikai információk naplózására és a késéssel kapcsolatos hibák elhárítására, ahogy az az alábbi példában is látható. Naplózhatja a diagnosztikai karakterláncot olyan kérésekhez, amelyek nagyobb olvasási késéssel rendelkeznek. A rögzített diagnosztikai karakterlánc segít megérteni, hogy hányszor kapott 429 hibát egy adott kérelemnél.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Az alacsonyabb olvasási késéshez tartozó URI-k gyorsítótárazása**

Ha lehetséges, a legjobb olvasási teljesítmény érdekében gyorsítótárazza a dokumentumok URI-azonosítóit. Erőforrás létrehozásakor meg kell határoznia a logikát az erőforrás-azonosító gyorsítótárazásához. Az erőforrás-azonosítók alapján végzett keresések gyorsabbak, mint a név-alapú keresések, ezért az értékek gyorsítótárazása javítja a teljesítményt.

**A jobb teljesítmény érdekében a lekérdezések/olvasási hírcsatornák méretének finomhangolása**

Ha a dokumentumok tömeges beolvasása a hírcsatornák olvasási funkciójának használatával történik (például: `ReadDocumentFeedAsync` ), vagy ha SQL-lekérdezést ad ki, akkor az eredmények szegmentált módon lesznek visszaadva, ha az eredményhalmaz túl nagy. Alapértelmezés szerint a rendszer az eredményeket 100 elemből vagy 1 MB-ra adja vissza, amely a határértékek közül az első.

Ha csökkenteni szeretné az összes vonatkozó eredmény beolvasásához szükséges hálózati kör-átutazások számát, az [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) használatával megnövelheti az oldal méretét, hogy több mint 1 000 fejlécet kérjen. Ha csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az Application API egyszerre csak 10 találatot ad vissza, az olvasási és lekérdezési teljesítmény csökkentése érdekében csökkentheti az oldal méretét is 10 értékre.

> [!NOTE] 
> A `maxItemCount` tulajdonságot nem szabad csak a tördeléshez használni. Fő felhasználási célja a lekérdezések teljesítményének javítása azáltal, hogy csökkenti az egyetlen oldalon visszaadott elemek maximális számát.  

Az oldalméret az elérhető Azure Cosmos DB SDK-k használatával is beállítható. A [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount) tulajdonsága `FeedOptions` lehetővé teszi, hogy beállítsa a számbavételi műveletben visszaadott elemek maximális számát. Ha a `maxItemCount` értéke-1, az SDK automatikusan megkeresi az optimális értéket a dokumentum méretétől függően. Például:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Egy lekérdezés végrehajtásakor az eredményül kapott adatküldés egy TCP-csomagon belül történik. Ha túl alacsony értéket ad meg `maxItemCount` , a TCP-csomagban lévő adatküldéshez szükséges utak száma magas, ami hatással van a teljesítményre. Tehát ha nem biztos benne, hogy melyik értéket kell beállítania a `maxItemCount` tulajdonsághoz, akkor a legjobb, ha az-1 értékre állítja, és lehetővé teszi, hogy az SDK kiválassza az alapértelmezett értéket.

**A szálak/feladatok számának növelésével**

Lásd: [a szálak/feladatok számának növekedése](#increase-threads) a hálózatkezelés szakaszában.

## <a name="indexing-policy"></a>Indexelési szabályzat
 
**Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

A Azure Cosmos DB indexelési házirend azt is lehetővé teszi, hogy az indexelési elérési utak (IndexingPolicy. IncludedPaths és IndexingPolicy. ExcludedPaths) használatával megadhatja, hogy mely dokumentum-elérési utak szerepeljenek az indexelésben vagy kizárva. Az indexelési elérési utak javíthatják az írási teljesítményt, és csökkenthetik az indexek tárolását olyan helyzetekben, amikor a lekérdezési mintákat előre ismerik. Ennek az az oka, hogy az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával kapcsolatosak. Ez a kód például azt mutatja be, hogyan zárható ki a dokumentumok (egy részfa) teljes szakasza az indexelésből a "*" helyettesítő karakterrel:

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

További információ: [Azure Cosmos db indexelési házirendek](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Átviteli sebesség

**Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja. Ezek a műveletek a UDF, tárolt eljárásokkal és triggerekkel kapcsolatos, az adatbázis-gyűjteményen belüli összes műveletre vonatkozó viszonyítási és hierarchikus lekérdezéseket tartalmazzák. Az egyes műveletekhez kapcsolódó díjak a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához és az alkalmazásokra vonatkozó kérések kiszolgálásához szükséges erőforrásokhoz.

Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérelem egységének felhasználását a rendszer másodpercenkénti arányban értékeli. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt.

A lekérdezés összetettsége hatással van arra, hogy egy művelethez hány kérelemegység szükséges. A predikátumok száma, a predikátumok természete, a UDF száma és a forrás-adatkészlet mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

Bármilyen művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécét (vagy a `RequestCharge` .net SDK-ban vagy annak megfelelő tulajdonságát `ResourceResponse\<T>` ) a `FeedResponse\<T>` műveletek által felhasznált kérelmek mennyiségének méréséhez:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség (azaz 2 000 RUs/s) része. Ha például az előző lekérdezés 1 000 1 – KB dokumentumokat ad vissza, a művelet díja 1 000. Tehát egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló a RequestRateTooLarge (429-as HTTP-állapotkód) végére fogja megelőző jelleggel a kérést. Egy [x-MS-újrapróbálkozás-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet ad vissza, amely megadja, hogy a felhasználónak mennyi ideig kell várnia, mielőtt újra megkísérli a kérést.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a maximálisan engedélyezett műveleteket, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó DocumentClientException hoz az alkalmazáshoz. 

Az újrapróbálkozások alapértelmezett számának módosításához állítsa a `RetryOptions` példányt a `ConnectionPolicy` példányra. Alapértelmezés szerint a 429-as kódú DocumentClientException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez a hiba akkor is igaz, ha az újrapróbálkozások száma nem éri el az újrapróbálkozások maximális számát, az aktuális érték pedig az alapértelmezett 9 vagy felhasználó által definiált érték.

Az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását. Előfordulhat azonban, hogy a teljesítmény-teljesítménymutatók végrehajtásakor nem a legjobb viselkedés, különösen ha a késés mérését végzi. Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.

**Nagyobb átviteli sebesség esetén a kisebb dokumentumok tervezése**

Egy adott műveletre vonatkozó kérelem díja (azaz a kérelmek feldolgozási díja) közvetlenül a dokumentum méretével összefügg. A nagyméretű dokumentumokon végzett műveletek többek között a kis dokumentumokon végzett műveletekhez szükségesek.

## <a name="next-steps"></a>Következő lépések

Az egyes ügyfélszámítógépeken a nagy teljesítményű forgatókönyvek Azure Cosmos DB kiértékeléséhez használt minta alkalmazással kapcsolatban lásd: [teljesítmény-és méretezési tesztek a Azure Cosmos db](performance-testing.md)használatával.

Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partitioning-overview.md)című témakört.