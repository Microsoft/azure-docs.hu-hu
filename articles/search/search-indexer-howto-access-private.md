---
title: Indexelő kapcsolatai egy privát végponton keresztül
titleSuffix: Azure Cognitive Search
description: Az indexelő kapcsolatainak konfigurálásával más Azure-erőforrásoktól származó tartalmakat is elérhet, amelyeket privát végponton keresztül véd.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b30cb1767e733861d8418ea29e564bc90a5bc70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676515"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Indexelő kapcsolatainak elkészítése privát végponton keresztül

Számos Azure-erőforrás – például az Azure Storage-fiókok – úgy konfigurálható, hogy fogadjon kapcsolatokat a virtuális hálózatok listájáról, és elutasítja a nyilvános hálózatról származó kapcsolaton kívüli kapcsolatokat. Ha indexelő használatával indexeli az adatait az Azure Cognitive Searchban, és az adatforrás egy magánhálózaton található, létrehozhat egy kimenő [magánhálózati végponti kapcsolattal](../private-link/private-endpoint-overview.md) az adatelérést.

Az indexelő kapcsolódási módszere a következő két követelményre vonatkozik:

+ A tartalmat vagy kódot biztosító Azure-erőforrást előzőleg regisztrálni kell az [Azure Private link Service](https://azure.microsoft.com/services/private-link/)-ben.

+ Az Azure Cognitive Search szolgáltatásnak alapszintű vagy magasabb szintűnek kell lennie. A szolgáltatás nem érhető el az ingyenes szinten. Emellett, ha az indexelő készségkészlet rendelkezik, a szintnek Standard 2 (S2) vagy magasabb szintűnek kell lennie. További információ: [szolgáltatási korlátok](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Megosztott privát kapcsolatok erőforrásainak kezelési API-jai

Az Azure Cognitive Search API-kkal létrehozott biztonságos erőforrások privát végpontait *megosztott magánhálózati kapcsolati erőforrásoknak* nevezzük. Ennek az az oka, hogy az [Azure Private link szolgáltatással](https://azure.microsoft.com/services/private-link/)integrált erőforráshoz (például egy Storage-fiókhoz) való hozzáférés "megosztás".

A felügyeleti REST API az Azure Cognitive Search [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) műveletet biztosít, amellyel konfigurálhatja az Azure Cognitive Search indexelő szolgáltatáshoz való hozzáférést.

A következő táblázatban látható *előzetes* verzióban csak a Search Management API előzetes verziójával ( *2020-08-01-* es vagy újabb verzió) hozhatók létre privát végponti kapcsolatok bizonyos erőforrásokhoz. Az *előzetes* megjelölést nem tartalmazó erőforrások az előzetes verziójú vagy az általánosan elérhető API-verzióval (*2020-08-01* vagy újabb) hozhatók létre.

Az alábbi táblázat azokat az Azure-erőforrásokat sorolja fel, amelyekhez kimenő privát végpontokat hozhat létre az Azure Cognitive Searchból. Megosztott magánhálózati kapcsolati erőforrás létrehozásához pontosan írja be a **csoport azonosítójának** értékeit, ahogy azok az API-ban íródnak. Az értékek megkülönböztetik a kis-és nagybetűket.

| Azure-erőforrás | Csoportazonosító |
| --- | --- |
| Azure Storage – blob (vagy) ADLS Gen 2 | `blob`|
| Azure Storage – táblák | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (előzetes verzió) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (előzetes verzió) | `sites` |

A [támogatott API-k listájának](/rest/api/searchmanagement/privatelinkresources/listsupported)használatával lekérdezheti azokat az Azure-erőforrásokat is, amelyek esetében a kimenő magánhálózati végpontok kapcsolatai támogatottak.

A cikk további részében az [Azure CLI](/cli/azure/) (vagy a [ARMClient](https://github.com/projectkudu/ARMClient) , ha szeretné) és a [Poster](https://www.postman.com/) (vagy bármely más http-ügyfél, például a [curl](https://curl.se/) ) kombinációja az REST API-hívások bemutatására szolgál.

> [!NOTE]
> A cikkben szereplő példák a következő feltételezéseken alapulnak:
> * A Search szolgáltatás neve a _contoso-Search_, amely a _00000000-0000-0000-0000-000000000000_-os előfizetés-azonosítójú előfizetés _contoso_ -erőforráscsoporthoz tartozik. 
> * A keresési szolgáltatás erőforrás-azonosítója _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Search/searchServices/contoso-Search_.

A további példák azt mutatják be, hogy a _contoso-Search_ szolgáltatás hogyan konfigurálható úgy, hogy az indexelő hozzáférhessenek az adatokhoz a biztonságos Storage-fiók _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Storage/storageAccounts/contoso-Storage_.

## <a name="secure-your-storage-account"></a>A Storage-fiók biztonságossá tétele

Konfigurálja úgy a Storage-fiókot, hogy [csak adott alhálózatokról engedélyezze a hozzáférést](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Ha ezt a beállítást választja, és hagyja üresen a készletet, akkor a Azure Portalban nem engedélyezett a virtuális hálózatokról érkező forgalom.

   ![Képernyőkép a "tűzfalak és virtuális hálózatok" panelről, amely a kiválasztott hálózatokhoz való hozzáférés engedélyezésének lehetőségét mutatja. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> A [megbízható Microsoft-szolgáltatás megközelítésével](../storage/common/storage-network-security.md#trusted-microsoft-services) megkerülheti a Storage-fiók virtuális hálózatát vagy IP-korlátozásait. Azt is engedélyezheti, hogy a keresési szolgáltatás hozzáférjen a Storage-fiókban lévő adateléréshez. Ehhez lásd: [Indexelő hozzáférés az Azure Storage szolgáltatáshoz a megbízható szolgáltatási kivétellel](search-indexer-howto-access-trusted-service-exception.md). 
>
> Ha azonban ezt a módszert használja, az Azure Cognitive Search és a Storage-fiók közötti kommunikáció a Storage-fiók nyilvános IP-címén keresztül történik a biztonságos Microsoft gerinc-hálózaton keresztül.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>1. lépés: megosztott magánhálózati kapcsolati erőforrás létrehozása a Storage-fiókhoz

Ha szeretné, hogy az Azure Cognitive Search hozzon létre egy kimenő magánhálózati végponti kapcsolódást a Storage-fiókhoz, hajtsa végre az alábbi API-hívást, például az [Azure CLI](/cli/azure/)-vel: 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

Vagy ha szívesebben szeretné használni a [ARMClient](https://github.com/projectkudu/ARMClient)-t:

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

A fájl *create-pe.jsjának* tartalma, amely a kérés törzsét jelképezi az API-nak, a következők:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

A `202 Accepted` rendszer a sikeres válaszokat adja vissza. A kimenő magánhálózati végpont létrehozásának folyamata hosszú ideig futó (aszinkron) művelet. A következő erőforrások üzembe helyezését foglalja magában:

* Egy magánhálózati IP-címmel lefoglalt privát végpont `"Pending"` . A magánhálózati IP-címet a keresési szolgáltatás-specifikus privát indexelő számára a végrehajtási környezet virtuális hálózatához lefoglalt címtartomány alapján szerezték be. A privát végpont jóváhagyása után az Azure Cognitive Searchról a Storage-fiókra irányuló minden kommunikáció a magánhálózati IP-címről és egy biztonságos privát kapcsolati csatornából származik.

* Saját DNS-zóna az erőforrás típusához, a alapján `groupId` . Az erőforrás központi telepítésével gondoskodhat arról, hogy a privát erőforráshoz tartozó DNS-keresések a privát végponthoz társított IP-címet használják.

Ügyeljen arra, hogy pontosan adja meg annak `groupId` az erőforrásnak a típusát, amelyhez a privát végpontot létrehozza. Az eltérések nem megfelelő válaszüzenetet eredményeznek.

Ahogy az összes aszinkron Azure-művelet esetében, a `PUT` hívás egy olyan fejléc-értéket ad vissza, `Azure-AsyncOperation` amely a következőhöz hasonlóan néz ki:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Ezt az URI-t időnként lekérdezheti a művelet állapotának beszerzéséhez. A folytatás előtt azt javasoljuk, hogy várjon, amíg a megosztott magánhálózati kapcsolat erőforrás-művelete elérte a terminál állapotát (azaz a művelet állapota *sikeres* volt).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

Vagy a ARMClient használata:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>2a. lépés: a Storage-fiókhoz tartozó magánhálózati végponti kapcsolatok jóváhagyása

> [!NOTE]
> Ebben a szakaszban a Azure Portal segítségével végigvezeti a privát végpontok tárolásához szükséges jóváhagyási folyamaton. Másik lehetőségként használhatja a Storage erőforrás-szolgáltatón keresztül elérhető [REST API](/rest/api/storagerp/privateendpointconnections) .
>
> Más szolgáltatók, például a Azure Cosmos DB vagy az Azure SQL Server, hasonló tárolási erőforrás-szolgáltatói API-kat kínálnak a privát végponti kapcsolatok kezeléséhez.

1. A Azure Portal válassza ki a Storage-fiók **privát Endpoint Connections** lapját. Az aszinkron művelet sikeres végrehajtása után az előző API-híváshoz tartozó kéréssel rendelkező privát végponti kapcsolatok iránti kérelemnek kell lennie.

   ![Képernyőkép a Azure Portalről, amely a "Private Endpoint connections" panelt jeleníti meg.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Válassza ki azt a privát végpontot, amelyet az Azure Cognitive Search hozott létre. A **privát végpont** oszlopban azonosítsa a magánhálózati végponti kapcsolatokat az előző API-ban megadott névvel, válassza a **jóváhagyás** lehetőséget, majd adjon meg egy megfelelő üzenetet. Az üzenet tartalma nem jelentős. 

   Győződjön meg arról, hogy a magánhálózati végponti kapcsolatok a következő képernyőképen látható módon jelennek meg. A portálon az állapot frissítése egy-két percet is igénybe vehet.

   ![Képernyőkép a Azure Portalről, amely "jóváhagyva" állapotot mutat a "Private Endpoint connections" panelen.](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

A magánhálózati végponti kapcsolatfelvételi kérelem jóváhagyása után a forgalom *képes* a privát végponton keresztüli áthaladásra. A privát végpont jóváhagyása után az Azure Cognitive Search létrehozza a szükséges DNS-zónák hozzárendeléseit a hozzá létrehozott DNS-zónában.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>2b. lépés: a megosztott magánhálózati kapcsolati erőforrás állapotának lekérdezése

Annak ellenőrzéséhez, hogy a megosztott magánhálózati kapcsolati erőforrás jóváhagyása után frissült-e, szerezze be az állapotát a [Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get)használatával.

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Vagy a ARMClient használata:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Ha az `properties.provisioningState` erőforrás és az, az azt `Succeeded` `properties.status` `Approved` jelenti, hogy a megosztott magánhálózati kapcsolati erőforrás működik, és az indexelő úgy konfigurálható, hogy a magánhálózati végponton keresztül kommunikáljon.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>3. lépés: az indexelő konfigurálása a privát környezetben való futtatásra

> [!NOTE]
> Ezt a lépést a privát végponti kapcsolatok jóváhagyása előtt is elvégezheti. A magánhálózati végponti kapcsolatok jóváhagyása előtt minden olyan indexelő, amely egy biztonságos erőforrással (például a Storage-fiókkal) kommunikálni próbál, egy átmeneti meghibásodási állapotba kerül. Az új indexelő létrehozása sikertelen lesz. A privát végponti kapcsolat jóváhagyása után az indexelő hozzáférhetnek a privát Storage-fiókhoz.

1. [Hozzon létre egy adatforrást](/rest/api/searchservice/create-data-source) , amely a biztonságos Storage-fiókra és a Storage-fiókban található megfelelő tárolóra mutat. Az alábbi képernyőfelvételen ezt a kérelmet láthatja postás nyelven.

   ![A Poster felhasználói felületén található adatforrás létrehozását bemutató képernyőkép.](media\search-indexer-howto-secure-access\create-ds.png )

1. Hasonlóképpen [hozzon létre egy indexet](/rest/api/searchservice/create-index) , és szükség esetén [hozzon létre egy készségkészlet](/rest/api/searchservice/create-skillset) a REST API használatával.

1. [Hozzon létre egy indexelő](/rest/api/searchservice/create-indexer) , amely az előző lépésben létrehozott adatforrásra, indexre és készségkészlet mutat. Emellett kényszerítse az indexelő futtatását a privát végrehajtási környezetben úgy, hogy az indexelő konfigurációs tulajdonságát a értékre állítja `executionEnvironment` `private` .

   ![Képernyőfelvétel: az indexelő létrehozása a Poster felhasználói felületen.](media\search-indexer-howto-secure-access\create-idr.png)

   Az indexelő sikeres létrehozása után meg kell kezdenie a tartalom indexelését a Storage-fiókból a magánhálózati végponti kapcsolaton keresztül. Az indexelő állapotát az [Indexelő status API](/rest/api/searchservice/get-indexer-status)használatával figyelheti.

> [!NOTE]
> Ha már rendelkezik meglévő indexelő szolgáltatásokkal, ezeket a [put API](/rest/api/searchservice/create-indexer) -val frissítheti, ha beállítja a `executionEnvironment` to értéket `private` .

## <a name="troubleshooting"></a>Hibaelhárítás

- Ha az indexelő létrehozása a következő hibaüzenettel meghiúsul: "az adatforrás hitelesítő adatai érvénytelenek", akkor az azt jelenti, hogy a magánhálózati végponti kapcsolat állapota még nincs *jóváhagyva* , vagy a kapcsolat nem működik. A probléma megoldásához: 
  * Szerezze be a megosztott magánhálózati kapcsolati erőforrás állapotát a [Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get)használatával. Ha az állapot *jóváhagyva*, ellenőrizze az `properties.provisioningState` erőforrást. Ha itt az állapot látható `Incomplete` , az azt jelenti, hogy az erőforráshoz tartozó mögöttes függőségek némelyikét nem sikerült beállítani. Ha újra ki `PUT` szeretné állítani a megosztott privát kapcsolati erőforrás újbóli létrehozásához szükséges kérelmet, javítsa ki a problémát. Szükség lehet újrajóváhagyásra. Az erőforrás állapotának ellenőrzésével ellenőrizheti, hogy a probléma kijavítva lett-e.

- Ha a tulajdonság beállítása nélkül hozza létre az indexet, `executionEnvironment` a létrehozás sikeres lehet, de a végrehajtási előzmények azt mutatják, hogy az indexelő futtatása sikertelen. A probléma megoldásához:
   * Az [Indexelő frissítése](/rest/api/searchservice/update-indexer) a végrehajtási környezet megadásához.

- Ha a tulajdonság beállítása nélkül hozta létre az indexelő `executionEnvironment` eszközt, és sikeresen fut, az azt jelenti, hogy az Azure Cognitive Search úgy döntött, hogy végrehajtási környezete a keresési szolgáltatásra jellemző *privát* környezet. Ez az indexelő által felhasznált erőforrásoktól, a keresési szolgáltatás terhelésének és más tényezőktől függően változhat, és később is sikertelen lehet. A probléma megoldásához:
  * Javasoljuk, hogy a tulajdonságot úgy állítsa be, hogy a `executionEnvironment` `private` jövőben ne legyen sikertelen.

A [kvóták és a korlátok](search-limits-quotas-capacity.md) határozzák meg, hogy hány megosztott magánhálózati kapcsolati erőforrás hozható létre, és függ a keresési szolgáltatás SKU-jának.

## <a name="next-steps"></a>Következő lépések

További információ a privát végpontokról:

- [Mik azok a privát végpontok?](../private-link/private-endpoint-overview.md)
- [Magánhálózati végpontokhoz szükséges DNS-konfigurációk](../private-link/private-endpoint-dns.md)