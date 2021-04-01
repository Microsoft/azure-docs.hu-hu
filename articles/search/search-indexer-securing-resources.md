---
title: Indexelő hozzáférése a védett erőforrásokhoz
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search indexelő szolgáltatásával elérhető hálózati szintű biztonsági beállítások fogalmi áttekintése.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92503487"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Indexelő hozzáférés az Azure hálózati biztonsági funkciói által védett tartalmakhoz (Azure Cognitive Search)

Az Azure Cognitive Search-indexelő a végrehajtás során különböző Azure-erőforrásokhoz is elvégezhetik a kimenő hívásokat. Ez a cikk az indexelő által az IP-tűzfalak, a privát végpontok vagy más Azure hálózati szintű biztonsági mechanizmusok által védett tartalomhoz való hozzáférés hátterét ismerteti. Az indexelő két helyzetben teszi a kimenő hívásokat: az indexelés során csatlakozik az adatforrásokhoz, és egy készségkészlet keresztül csatlakozik a beágyazott kódhoz. Az alábbi táblázat felsorolja az összes lehetséges erőforrástípus listáját, amelyet egy indexelő elér egy tipikus futtatásban.

| Erőforrás | Az indexelő futtatásának célja |
| --- | --- |
| Azure Storage (Blobok, táblák, ADLS Gen 2) | Adatforrás |
| Azure Storage (Blobok, táblák) | Szakértelmével (a bővített dokumentumok gyorsítótárazása és a Knowledge Store-előrejelzések tárolása) |
| Azure Cosmos DB (különböző API-k) | Adatforrás |
| Azure SQL Database | Adatforrás |
| Azure-beli virtuális gépen futó SQL Server | Adatforrás |
| SQL Managed Instance | Adatforrás |
| Azure Functions | Az egyéni webes API-ismeretek üzemeltetése |
| Cognitive Services | Csatolva a készségkészlet-hez, amely a 20 szabad dokumentumra vonatkozó korláton felüli kiszámlázást fogja használni |

> [!NOTE]
> A készségkészlet csatolt kognitív szolgáltatási erőforrás a számlázásra szolgál, a keresési indexbe írt alkoholtartalom-növelés alapján. Nem használatos a Cognitive Services API-khoz való hozzáféréshez. Az indexelő bővítési folyamata Cognitive Services API-k egy belső biztonságos kommunikációs csatornán keresztül történik, ahol az adatok erősen titkosítottak az átvitel során, és soha nem tárolják a nyugalmi állapotban.

Az ügyfelek az Azure által kínált számos hálózati elkülönítési mechanizmuson keresztül biztonságossá tehetik ezeket az erőforrásokat. A kognitív szolgáltatások erőforrásának kivételével az indexelő korlátozott mértékben képesek hozzáférni az összes többi erőforráshoz, még akkor is, ha azok hálózatról elkülönítettek, az alábbi táblázatban ismertetett lépések szerint.

| Erőforrás | IP-korlátozás | Privát végpont |
| --- | --- | ---- |
| Azure Storage (Blobok, táblák, ADLS Gen 2) | Csak akkor támogatott, ha a Storage-fiók és a keresési szolgáltatás különböző régiókban található | Támogatott |
| Azure Cosmos DB-SQL API | Támogatott | Támogatott |
| Azure Cosmos DB-Cassandra, Mongo és Gremlin API | Támogatott | Nem támogatott |
| Azure SQL Database | Támogatott | Támogatott |
| Azure-beli virtuális gépen futó SQL Server | Támogatott | N/A |
| SQL Managed Instance | Támogatott | N/A |
| Azure Functions | Támogatott | Támogatott, csak az Azure functions egyes szintjei esetében |

> [!NOTE]
> A fent felsorolt lehetőségek mellett a hálózattal védett Azure Storage-fiókok esetében az ügyfelek kihasználhatják azt a tényt, hogy az Azure Cognitive Search [megbízható Microsoft-szolgáltatás](../storage/common/storage-network-security.md#trusted-microsoft-services). Ez azt jelenti, hogy egy adott keresési szolgáltatás megkerülheti a virtuális hálózat vagy az IP-korlátozásokat a Storage-fiókban, és hozzáférhet a Storage-fiókban található adathoz, ha a megfelelő szerepköralapú hozzáférés-vezérlés engedélyezve van a Storage-fiókban. További információ: [Indexelő kapcsolatok a megbízható szolgáltatással kapcsolatos kivétel használatával](search-indexer-howto-access-trusted-service-exception.md). Ez a beállítás az IP-korlátozási útvonal helyett használható, abban az esetben, ha a Storage-fiók vagy a keresési szolgáltatás nem helyezhető át másik régióba.

Az indexelő által használandó biztonságos hozzáférési mechanizmus kiválasztásakor vegye figyelembe a következő korlátozásokat:

- Az indexelő nem tud csatlakozni a [virtuális hálózati szolgáltatás végponthoz](../virtual-network/virtual-network-service-endpoints-overview.md). A hitelesítő adatokkal, privát végpontokkal, megbízható szolgáltatásokkal és IP-címzéssel rendelkező nyilvános végpontok az indexelő kapcsolatainak egyetlen támogatott módszerei.
- Egy keresési szolgáltatás nem építhető ki egy adott virtuális hálózatra, amely natív módon fut egy virtuális gépen. Ezt a funkciót az Azure Cognitive Search nem fogja ajánlani.
- Ha az indexelő (kimenő) magánhálózati végpontokat használ az erőforrásokhoz való hozzáféréshez, előfordulhat, hogy további [privát kapcsolati költségek](https://azure.microsoft.com/pricing/details/search/) is érvényesek.

## <a name="indexer-execution-environment"></a>Indexelő végrehajtási környezet

Az Azure Cognitive Search indexelő képes hatékonyan kinyerni a tartalmakat az adatforrásokból, bővíteni a kinyert tartalmat, és opcionálisan generálja az eredményeket a keresési indexbe való írás előtt. Az indexelő által hozzárendelt felelősségek számától függően a következő két környezet egyikében futhat:

- Egy adott keresési szolgáltatáshoz tartozó saját környezet. Az ilyen környezetekben futó indexelő más számítási feladatokkal (például más ügyfél által kezdeményezett indexelési vagy lekérdezési számítási feladatokkal) oszthatják meg az erőforrásokat. Általában csak szöveges indexelést végrehajtó indexelő (például nem használnak készségkészlet) futnak ebben a környezetben.
- Több-bérlős környezet, amely erőforrás-igényes, például szakértelmével rendelkező indexelő üzemeltet. Ez a környezet a számítási szempontból intenzív feldolgozás kiszervezésére szolgál, így a szolgáltatás-specifikus erőforrások nem állnak rendelkezésre a rutin működéséhez. Ezt a több-bérlős környezetet a Microsoft felügyeli és védi, és az ügyfél számára külön díj nélkül.

Az Azure Cognitive Search az indexelő futtatásához szükséges legjobb környezetet határozza meg. Ha IP-tűzfalat használ az Azure-erőforrásokhoz való hozzáférés szabályozására, akkor a végrehajtási környezetek ismerete segítséget nyújt a mindkettőt magában foglaló IP-címtartomány beállításához.

## <a name="granting-access-to-indexer-ip-ranges"></a>Hozzáférés biztosítása az indexelő IP-tartományokhoz

Ha az indexelő által elérni kívánt erőforrás csak az IP-címtartományok bizonyos készletére korlátozódik, akkor ki kell bővíteni a készletet, hogy tartalmazza a lehetséges IP-tartományokat, amelyekből az indexelő kérelme származhat. Ahogy azt fentebb említettük, két lehetséges környezet létezik, amelyekben az indexelő fut, és ahonnan a hozzáférési kérelmek származhatnak. **Mindkét** környezet IP-címét fel kell vennie az indexelő hozzáférésének működéséhez.

- A keresési szolgáltatás megadott privát környezetének IP-címének `nslookup` (vagy `ping` ) a keresési szolgáltatás teljes tartománynevének (FQDN) beszerzése. A nyilvános felhőben található keresési szolgáltatás teljes tartományneve például a következő lesz: `<service-name>.search.windows.net` . Ezek az információk a Azure Portal érhetők el.
- A több-bérlős környezetek IP-címei a `AzureCognitiveSearch` szolgáltatás címkéjén keresztül érhetők el. Az [Azure-szolgáltatás címkéi](../virtual-network/service-tags-overview.md) az egyes szolgáltatások IP-címeinek közzétett tartományával rendelkeznek – ez egy [felderítési API-n (előzetes verzió)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) vagy egy [letölthető JSON-fájlon](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)keresztül érhető el. Mindkét esetben az IP-címtartományok régiónként vannak lebontva – csak azokat az IP-tartományokat választhatja ki, amelyek ahhoz a régióhoz vannak rendelve, amelyben a keresési szolgáltatást kiépítik.

Bizonyos adatforrások esetében maga a szolgáltatási címke közvetlenül is használható az IP-címtartományok listájának enumerálása helyett (a keresési szolgáltatás IP-címét explicit módon kell használni). Ezek az adatforrások korlátozzák a hozzáférést egy olyan [hálózati biztonsági csoport szabályának](../virtual-network/network-security-groups-overview.md)beállításával, amely natív módon támogatja a szolgáltatási címkék hozzáadását, az olyan IP-szabályoktól eltérően, mint az Azure Storage, az Cosmos db, az Azure SQL stb. Azok az adatforrások, amelyek támogatják a `AzureCognitiveSearch` szolgáltatás címkéjének közvetlen használatát a keresési szolgáltatás IP-címe mellett, a következők:

- [SQL Server Azure-beli virtuális gépeken](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Felügyelt SQL-példányok](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

További információ erről a kapcsolati lehetőségről: [Indexelő kapcsolatok IP-tűzfalon keresztül](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Hozzáférés biztosítása privát végpontokon keresztül

Az indexelő használhatnak [privát végpontokat](../private-link/private-endpoint-overview.md) az erőforrásokhoz való hozzáféréshez, a hozzáféréshez, amelyekhez a virtuális hálózatokat kiválasztva vagy nincs engedélyezve a nyilvános hozzáférés.
Ez a funkció csak a számlázható keresési szolgáltatásokban érhető el, a létrehozott magánhálózati végpontok számának korlátozásával. További információ: [szolgáltatási korlátok](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>1. lépés: hozzon létre egy privát végpontot a biztonságos erőforráshoz

Az ügyfeleknek meg kell hívniuk a keresési kezelési műveletet, a [CREATEORUPDATE API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) -t egy **megosztott privát kapcsolati erőforráson**, hogy létre lehessen hozni egy privát végponti kapcsolatot a biztonságos erőforráshoz (például egy Storage-fiókhoz). Az erre a (kimenő) privát végponti kapcsolatra irányuló forgalom csak a keresési szolgáltatásban megadott "privát" indexelő végrehajtási környezetben található virtuális hálózatból származik.

Az Azure Cognitive Search ellenőrzi, hogy az API hívói rendelkeznek-e Azure RBAC engedéllyel a privát végpontok kapcsolódási kéréseinek a biztonságos erőforráshoz való jóváhagyásához. Ha például egy csak olvasási jogosultsággal rendelkező Storage-fiókhoz kér privát végponti kapcsolatokat, a rendszer a hívást elutasítja.

### <a name="step-2-approve-the-private-endpoint-connection"></a>2. lépés: a magánhálózati végponti kapcsolatok jóváhagyása

Ha a megosztott magánhálózati kapcsolati erőforrást létrehozó (aszinkron) művelet befejeződik, a rendszer egy privát végponti kapcsolatot hoz létre "függőben" állapotba. Még nem folyik forgalom a kapcsolaton keresztül.
Az ügyfél ezután megkeresi a kérést a biztonságos erőforráson, és "jóváhagyja". Ez általában a Azure Portalon vagy a [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection)keresztül végezhető el.

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>3. lépés: az indexelő kényszerített futtatása a "privát" környezetben

A jóváhagyott privát végpontok lehetővé teszik a keresési szolgáltatás kimenő hívásait olyan erőforrásra, amely valamilyen hálózati szintű hozzáférési korlátozással rendelkezik (például egy olyan Storage-fiók adatforrása, amely csak bizonyos virtuális hálózatokról érhető el) a sikeres elérés érdekében.
Ez azt jelenti, hogy a privát végponton keresztül egy ilyen adatforráshoz hozzáférő indexelő is sikeres lesz.
Ha a titkos végpont nincs jóváhagyva, vagy ha az indexelő nem használja fel a magánhálózati végponti kapcsolatokat, akkor az indexelő futtatja a-t `transientFailure` .

Ha engedélyezni szeretné az indexelő számára, hogy privát végponti kapcsolatokon keresztül hozzáférjenek az erőforrásokhoz, akkor kötelező beállítani az indexelő annak érdekében, hogy az `executionEnvironment` `"Private"` Indexelő minden futtatása képes legyen használni a privát végpontot. Ennek az az oka, hogy a privát végpontok a privát keresési szolgáltatás-specifikus környezetben vannak kiépítve.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Ezeket a lépéseket részletesebben ismertetjük az [Indexelő kapcsolatain egy privát végponton keresztül](search-indexer-howto-access-private.md).
Miután jóváhagyta a privát végpontot egy erőforráshoz, az indexelő úgy van beállítva, hogy *a privát* végponti kapcsolaton keresztül hozzáférjen a hozzáféréshez.

### <a name="limits"></a>Korlátok

A keresési szolgáltatás optimális teljesítményének és stabilitásának biztosítása érdekében a korlátozásokat (a keresési szolgáltatási szintek alapján) a következő dimenziókban kell megszabni:

- *Magánjellegűként* beállítható indexelő típus.
- A létrehozható megosztott magánhálózati kapcsolati erőforrások száma.
- Azon különálló erőforrástípusok száma, amelyekhez megosztott magánhálózati kapcsolati erőforrások hozhatók létre.

Ezek a korlátok a [szolgáltatási korlátokban](search-limits-quotas-capacity.md)vannak dokumentálva.

## <a name="next-steps"></a>Következő lépések

- [Indexelő kapcsolatok IP-tűzfalakon keresztül](search-indexer-howto-access-ip-restricted.md)
- [Indexelő kapcsolatok a megbízható szolgáltatással kapcsolatos kivétel használatával](search-indexer-howto-access-trusted-service-exception.md)
- [Indexelő kapcsolatai egy privát végponthoz](search-indexer-howto-access-private.md)