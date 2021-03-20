---
title: Indexelő IP-címtartományok elérésének engedélyezése
titleSuffix: Azure Cognitive Search
description: Konfigurálja az IP-tűzfalszabályok használatát, hogy engedélyezze az Azure Cognitive Search indexelő számára az adatelérést.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757243"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>IP-tűzfalszabályok konfigurálása az indexelő kapcsolatainak engedélyezéséhez (Azure Cognitive Search)

Az Azure-erőforrások (például a Storage-fiókok, a Cosmos DB-fiókok és az Azure SQL-kiszolgálók) IP-tűzfalszabályok csak adott IP-tartományokból származó forgalmat engedélyezik az adatok eléréséhez.

Ez a cikk azt ismerteti, hogyan konfigurálható az IP-szabályok Azure Portalon keresztül egy Storage-fiókhoz, hogy az Azure Cognitive Search indexelő biztonságosan hozzáférhessenek az adatkezeléshez. Az Azure Storage-ra jellemző módon a módszer más Azure-erőforrások esetében is működik, amelyek IP-tűzfalszabályok használatával biztosítják az adathozzáférés biztosítását.

> [!NOTE]
> A Storage-fiók IP-tűzfalszabályok csak akkor lépnek érvénybe, ha a Storage-fiók és a keresési szolgáltatás különböző régiókban található. Ha a telepítő nem engedélyezi ezt a beállítást, javasoljuk, hogy használja a [megbízható szolgáltatás kivételét](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>A keresési szolgáltatás IP-címének beolvasása

Szerezze be a keresési szolgáltatás teljes tartománynevét (FQDN). Ez a következőképpen fog kinézni: `<search-service-name>.search.windows.net` . A teljes tartománynevet úgy tekintheti meg, ha megkeresi a keresési szolgáltatást a Azure Portalon.

   ![Szolgáltatás teljes tartománynevének beszerzése](media\search-indexer-howto-secure-access\search-service-portal.png "Szolgáltatás teljes tartománynevének beszerzése")

A keresési szolgáltatás IP-címe `nslookup` a teljes tartománynév (vagy a) használatával szerezhető be `ping` . Az alábbi példában a "150.0.0.1" kifejezést adja hozzá egy bejövő szabályhoz az Azure Storage-tűzfalon. A tűzfalbeállítások frissítése után akár 15 percet is igénybe vehet, hogy a keresési szolgáltatás indexelő hozzáférhessen az Azure Storage-fiókhoz.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Az IP-címtartományok beolvasása a "AzureCognitiveSearch" szolgáltatás címkéjéhez

A további IP-címek az indexelő [több-bérlős végrehajtási környezetből](search-indexer-securing-resources.md#indexer-execution-environment)származó kérelmekhez használatosak. Ezt az IP-címtartományt a szolgáltatás címkéjéből érheti el.

A szolgáltatás címkéjének IP-címtartományok a `AzureCognitiveSearch` [Discovery API (előzetes verzió)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) vagy a [letölthető JSON-fájl](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)segítségével szerezhetők be.

Ebben az útmutatóban, feltéve, hogy a Search szolgáltatás az Azure nyilvános felhő, le kell tölteni az [Azure nyilvános JSON-fájlját](https://www.microsoft.com/download/details.aspx?id=56519) .

   ![JSON-fájl letöltése](media\search-indexer-howto-secure-access\service-tag.png "JSON-fájl letöltése")

A JSON-fájlból, feltéve, hogy a Search szolgáltatás az USA nyugati középső régiójában található, az alábbi lista tartalmazza a több-bérlős indexelő végrehajtási környezet IP-címeinek listáját.

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

A/32 IP-címek esetében dobja el a "/32" (52.253.133.74/32-> 52.253.133.74), mások pedig szó szerint használhatók.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Az IP-címtartományok hozzáadása az IP-tűzfalszabályok számára

Az IP-címtartományok a Storage-fiók tűzfalszabály-szabályához való hozzáadásának legegyszerűbb módja a Azure Portal. Keresse meg a Storage-fiókot a portálon, és navigáljon a **tűzfalak és a virtuális hálózatok** lapra.

   ![Tűzfal és virtuális hálózatok](media\search-indexer-howto-secure-access\storage-firewall.png "Tűzfal és virtuális hálózatok")

Adja hozzá a korábban beszerzett három IP-címet (1 a keresési szolgáltatás IP-címéhez, 2 a `AzureCognitiveSearch` szolgáltatási címkéhez) a címtartomány területen, majd válassza a **Mentés** lehetőséget.

   ![Tűzfal IP-szabályai](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Tűzfal IP-szabályai")

A tűzfalszabályok 5-10 percet vesznek igénybe, és azt követően, hogy mely indexelő férhetnek hozzá a Storage-fiókban lévő információhoz.

## <a name="next-steps"></a>Következő lépések

- [Azure Storage-tűzfalak konfigurálása](../storage/common/storage-network-security.md)
- [A Cosmos DB IP-tűzfalának konfigurálása](../cosmos-db/how-to-configure-firewall.md)
- [Az Azure SQL Server IP-tűzfalának konfigurálása](../azure-sql/database/firewall-configure.md)