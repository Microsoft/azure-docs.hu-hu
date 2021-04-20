---
title: A keresési indexelővel kapcsolatos gyakori problémák elhárítása
titleSuffix: Azure Cognitive Search
description: Javítsa ki a hibákat és az indexelők gyakori problémáit a Azure Cognitive Search, beleértve az adatforrás-kapcsolatot, a tűzfalat és a hiányzó dokumentumokat.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740059"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Az indexelővel kapcsolatos gyakori problémák elhárítása a Azure Cognitive Search

Az indexelők számos problémát okozhatnak, amikor adatokat indexelnek a Azure Cognitive Search. A hibák fő kategóriái a következők:

* [Csatlakozás adatforráshoz vagy más erőforrásokhoz](#connection-errors)
* [Dokumentumfeldolgozás](#document-processing-errors)
* [Indexbe való dokumentumbe való be- és bebe-](#index-errors)

## <a name="connection-errors"></a>Csatlakozási hibák

> [!NOTE]
> Az indexelők korlátozott mértékben támogatják az adatforrások és az Azure hálózati biztonsági mechanizmusai által biztosított egyéb erőforrásokhoz való hozzáférést. Az indexelők jelenleg csak a megfelelő IP-címtartomány-korlátozási mechanizmusokon vagy NSG-szabályokon keresztül férhetnek hozzá az adatforráshoz, ha vannak. Az egyes támogatott adatforrások elérésének részletei az alábbiakban találhatók.
>
> A keresési szolgáltatás IP-címét a teljes tartománynevének (pl. ) pingelésével keresheti `<your-search-service-name>.search.windows.net` meg.
>
> A szolgáltatáscímke IP-címtartományát a Letölthető `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) [JSON-fájlok](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) vagy a Service Tag Discovery API használatával [találhatja meg.](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) Az IP-címtartomány hetente frissül.

### <a name="configure-firewall-rules"></a>Tűzfalszabályok konfigurálása

Az Azure Storage, a CosmosDB és a Azure SQL konfigurálható tűzfalat biztosítanak. Ha a tűzfal engedélyezve van, nem jelenik meg konkrét hibaüzenet. A tűzfallal kapcsolatos hibák általában általánosak, és a vagy a `The remote server returned an error: (403) Forbidden` `Credentials provided in the connection string are invalid or have expired` hasonlóak.

Az ilyen példányok indexelőinek 2 lehetőség van arra, hogy hozzáférjenek ezekhez az erőforrásokhoz:

* Tiltsa le a tűzfalat a Minden hálózat **hozzáférésének engedélyezésével** (ha ez lehetséges).
* Másik lehetőségként engedélyezheti a hozzáférést a keresési szolgáltatás IP-címére és a szolgáltatáscímke IP-címtartományára az erőforrás tűzfalszabályában `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) (IP-címtartomány korlátozása).

Az IP-címtartomány-korlátozások adatforrás-típusra vonatkozó konfigurálásával kapcsolatos részleteket az alábbi hivatkozásokon talál:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Korlátozás:** Az Azure Storage dokumentációjában leírt módon az IP-címtartomány korlátozásai csak akkor működnek, ha a keresési szolgáltatás és a tárfiók különböző régiókban van.

Az Azure Functions (amely [](cognitive-search-custom-skill-web-api.md)egyéni webes API-képességként is használható) szintén támogatja az [IP-címkorlátozásokat.](../azure-functions/ip-addresses.md#ip-address-restrictions) A konfigurálható IP-címek listája a keresési szolgáltatás IP-címe és a szolgáltatáscímke `AzureCognitiveSearch` IP-címtartománya lesz.

Az Azure-beli virtuális gépen található SQL Serveren található adatok elérésének részletei itt vannak [ismertetve](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Hálózati biztonsági csoport (NSG) szabályainak konfigurálása

Ha felügyelt SQL-példányban fér hozzá az adatokhoz, vagy ha egy Azure-beli virtuális gépet használ webszolgáltatás URI-ként egy egyéni webes API-képességhez, az ügyfeleknek nem kell adott IP-címekkel aggódniuk. [](cognitive-search-custom-skill-web-api.md)

Ilyen esetekben az Azure-beli virtuális gép vagy a felügyelt SQL-példány konfigurálható úgy, hogy egy virtuális hálózaton belül legyen. Ezután egy hálózati biztonsági csoport konfigurálható a virtuális hálózati alhálózatok és hálózati adapterek be- és kifelé áramló hálózati forgalmának szűrésére.

A szolgáltatáscímke közvetlenül használható a bejövő NSG-szabályokban anélkül, hogy ki kellene keresnie az `AzureCognitiveSearch` IP-címtartományát. [](../virtual-network/manage-network-security-group.md#work-with-security-rules)

A felügyelt SQL-példányok adatainak elérésével kapcsolatos további részletek itt [vannak ismertetve](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>A CosmosDB "indexelése" nincs engedélyezve

Azure Cognitive Search implicit függősége van a Cosmos DB indexeléstől. Ha kikapcsolja az automatikus indexelést a Cosmos DB, Azure Cognitive Search sikeres állapotot ad vissza, de nem tudja indexelni a tároló tartalmát. Útmutatás a beállítások ellenőrzéséhez és az indexelés bekapcsoláshoz: [Indexelés kezelése](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal)a Azure Cosmos DB.

### <a name="sharepoint-online-conditional-access-policies"></a>SharePoint Online feltételes hozzáférési szabályzatok

SharePoint Online-indexelő létrehozásakor végig fog menni egy lépésen, amely megköveteli az AAD-alkalmazásba való bejelentkezést az eszközkód megszakorálását követően. Ha ["A](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview) bejelentkezés sikeres volt, de a rendszergazdának szüksége van a hozzáférésre kérő eszközre" üzenet jelenik meg, az indexelő valószínűleg le lesz tiltva a SharePoint Online-dokumentumtárhoz való hozzáférésben egy feltételes hozzáférési szabályzat miatt.

Ha frissítenie kell a szabályzatot, hogy engedélyezze az indexelő számára a dokumentumtárhoz való hozzáférést, kövesse az alábbi lépéseket:

1. Nyissa meg Azure Portal, és keressen rá az **Azure AD feltételes hozzáférésre,** majd válassza a bal oldali menü **Szabályzatok** parancsát. Ha nem rendelkezik hozzáféréssel az oldal megtekintéséhez, meg kell találnia valakit, aki hozzáféréssel rendelkezik, vagy hozzáférést kell kapnia.

1. Határozza meg, hogy melyik szabályzat akadályozza meg, hogy a SharePoint Online-indexelő hozzáférjen a dokumentumtárhoz. Az indexelőt blokkoló szabályzat tartalmazza az indexelő létrehozásakor a Felhasználók és csoportok szakaszban a hitelesítéshez használt felhasználói **fiókot.** A szabályzat olyan feltételekkel is **előfordulhat, amelyek:**
    * **Windows-platformok** korlátozása.
    * Mobilalkalmazások **és asztali ügyfelek korlátozása.**
    * Az **Eszközállapot beállítása** Legyen **Igen.**

1. Miután megerősítette, hogy egy szabályzat blokkolja az indexelőt, kivételt kell tennie az indexelő számára. A keresési szolgáltatás IP-címének lekérése.

    1. Szerezze be a keresési szolgáltatás teljes tartománynevét (FQDN). Ez így fog `<search-service-name>.search.windows.net` kinézni: . Az FQDN-t úgy keresheti meg, hogy megkeresi a keresési szolgáltatást a Azure Portal.

   ![Szolgáltatás teljes tartományának beszerzése](media\search-indexer-howto-secure-access\search-service-portal.png "Szolgáltatás teljes tartományának beszerzése")

    A keresési szolgáltatás IP-címe az `nslookup` FQDN egy (vagy ) műveletével `ping` szerezhető be. Az alábbi példában a "150.0.0.1" et hozzáadná egy bejövő szabályhoz az Azure Storage-tűzfalon. Akár 15 percet is igénybe vehet, hogy a keresési szolgáltatás indexelője hozzáférjen az Azure Storage-fiókhoz a tűzfal beállításainak frissítése után.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Szerezze be a régió indexelő-végrehajtási környezetének IP-címtartományát.

    A rendszer további IP-címeket használ az indexelő több-bérlős végrehajtási környezetéből származó [kérések esetén.](search-indexer-securing-resources.md#indexer-execution-environment) Ezt az IP-címtartományt a szolgáltatáscímke alapján kaphatja meg.

    A szolgáltatáscímke IP-címtartományai a felderítési `AzureCognitiveSearch` [API-n (előzetes verzió)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) vagy a letölthető [JSON-fájlon keresztül szerezhetők be.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)

    Ebben az útmutatóban, feltéve, hogy a keresési szolgáltatás az Azure nyilvános felhője, le kell tölteni az [Azure nyilvános JSON-fájlt.](https://www.microsoft.com/download/details.aspx?id=56519)

   ![JSON-fájl letöltése](media\search-indexer-troubleshooting\service-tag.png "JSON-fájl letöltése")

    Ha a JSON-fájlban a keresési szolgáltatás az USA nyugati középső részén található, a több-bérlős indexelő végrehajtási környezet IP-címeinek listája alább látható.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. A feltételes hozzáférés lapra visszatérve Azure Portal  bal oldali menüben válassza a Nevestűs helyek lehetőséget, majd válassza az **+ IP-címtartományok helye lehetőséget.** Nevezze el az új elnevezett helyet, és adja hozzá a keresési szolgáltatás és az indexelő végrehajtási környezeteinek IP-címtartományát, amit az utolsó két lépésben gyűjtött össze.
    * Előfordulhat, hogy a keresési szolgáltatás IP-címének végén a "/32" címet kell hozzáadnia, mivel az csak érvényes IP-címtartományokat fogad el.
    * Ne feledje, hogy az indexelő-végrehajtási környezet IP-címtartományai esetén csak ahhoz a régióhoz kell HOZZÁADNIa az IP-címtartományokat, ahol a keresési szolgáltatás található.

1. Zárja ki az új Elnevezett helyet a szabályzatból. 
    1. A **bal oldali menüben** válassza a Szabályzatok lehetőséget. 
    1. Válassza ki azt a szabályzatot, amely blokkolja az indexelőt.
    1. Válassza a **Feltételek lehetőséget.**
    1. Válassza a **Helyek lehetőséget.**
    1. Válassza **az Exclude (Kizárás)** lehetőséget, majd adja hozzá az új Named location (Elnevezett hely) adatokat.
    1. **Mentse a** módosításokat.

1. Várjon néhány percet, amíg a szabályzat frissül és érvényesíti az új szabályzatszabályokat.

1. Próbálja meg ismét létrehozni az indexelőt
    1. Küldjön egy frissítési kérést a létrehozott adatforrás-objektumhoz.
    1. Küldje el újra az indexelő létrehozási kérelmét. Az új kóddal jelentkezzen be, majd a sikeres bejelentkezés után küldjön egy másik indexelő-létrehozási kérelmet.

## <a name="document-processing-errors"></a>Dokumentumfeldolgozási hibák

### <a name="unprocessable-or-unsupported-documents"></a>Feldolgozhatatlan vagy nem támogatott dokumentumok

A blobindexelő [dokumentumok, amelyek dokumentumformátumai kifejezetten támogatottak.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Előfordulhat, hogy egy Blob Storage-tároló nem támogatott dokumentumokat tartalmaz. Más esetekben problémás dokumentumok is előfordulhatnak. A konfigurációs beállítások módosításával elkerülheti az indexelő leállítását ezeken a [dokumentumokon:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Hiányzó dokumentumtartalom

A blobindexelő megkeresi és [kinyeri a szöveget a tárolóban lévő blobok alapján.](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs) Szöveg kinyerésével kapcsolatos problémák többek között a következők:

* A dokumentum csak beolvasott képeket tartalmaz. A nem szöveges tartalmú PDF-blobok, például a beolvasott képek (JPG-k) nem hoznak létre eredményeket szabványos blobindexelési folyamatban. Ha szöveges elemeket is tartalmaz képtartalmakhoz, kognitív kereséssel megkeresheti és kinyerheti a szöveget. [](cognitive-search-concept-image-scenarios.md)
* A blobindexelő úgy van konfigurálva, hogy csak a metaadatokat indexelje. A tartalom kinyerése érdekében a blobindexelőt úgy kell konfigurálni, hogy a tartalmat és a metaadatokat [is kinyerje:](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexhibák

### <a name="missing-documents"></a>Hiányzó dokumentumok

Az indexelők dokumentumokat keresnek egy [adatforrásból.](/rest/api/searchservice/create-data-source) Néha úgy tűnik, hogy egy indexből hiányzik egy olyan dokumentum az adatforrásból, amely indexelve lett volna. Ezek a hibák több gyakori okból is előfordulhatnak:

* A dokumentum nincs indexelve. Ellenőrizze a portálon, hogy sikeres volt-e az indexelő futtatása.
* Ellenőrizze a [változáskövetés értékét.](/rest/api/searchservice/create-data-source#data-change-detection-policies) Ha a felső vízjel értéke jövőbeli időpontra van beállítva, akkor az indexelő kihagy minden olyan dokumentumot, amely ennél kevesebb dátumot tartalmaz. Az indexelő állapot initialTrackingState és finalTrackingState mezőinek használatával megértheti az [indexelő változáskövetési állapotát.](/rest/api/searchservice/get-indexer-status#indexer-execution-result)
* A dokumentum az indexelő futtatása után frissült. Ha az indexelő ütemezés szerint [van,](/rest/api/searchservice/create-indexer#indexer-schedule)akkor a rendszer végül újrafuttatja és felveszi a dokumentumot.
* Az [adatforrásban](/rest/api/searchservice/create-data-source) megadott lekérdezés kizárja a dokumentumot. Az indexelők nem indexelnek olyan dokumentumokat, amelyek nem részei az adatforrásnak.
* [A mezőleképezések](/rest/api/searchservice/create-indexer#fieldmappings) vagy az AI-bővítődés módosították a dokumentumot, és a várttól eltérőnek tűnik. [](./cognitive-search-concept-intro.md)
* Keresse meg [a dokumentumot](/rest/api/searchservice/lookup-document) a keresési dokumentum API-jának használatával.