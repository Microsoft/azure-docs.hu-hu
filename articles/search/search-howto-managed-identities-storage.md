---
title: A Storage-fiókkal létesített kapcsolatok beállítása felügyelt identitás használatával
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan állíthat be indexelő-kapcsolatokat egy Azure Storage-fiókhoz felügyelt identitás használatával
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f26ca04955dfa854a8ee17b7aa255a6ed991b8df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358371"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>Azure Storage-tárfiókkal való kapcsolat beállítása felügyelt identitással

Ez a lap leírja, hogyan állíthat be egy Azure Storage-fiókhoz egy indexelő-kapcsolódást egy felügyelt identitás használatával, nem biztosítva hitelesítő adatokat az adatforrás-objektum kapcsolódási karakterláncában.

Mielőtt többet szeretne megtudni a szolgáltatásról, javasoljuk, hogy Ismerje meg, mi az indexelő, és hogyan állítható be egy indexelő az adatforráshoz. További információt az alábbi hivatkozásokon talál:
* [Az indexelő áttekintése](search-indexer-overview.md)
* [Azure Blob-indexelő](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 indexelő](search-howto-index-azure-data-lake-storage.md)
* [Azure Table indexelő](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>A kapcsolatok beállítása

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – a rendszerhez rendelt felügyelt identitás bekapcsolása

Ha egy rendszerhez rendelt felügyelt identitás engedélyezve van, az Azure létrehoz egy identitást a keresési szolgáltatáshoz, amely az ugyanazon a bérlőn és előfizetésen belüli más Azure-szolgáltatásokban való hitelesítéshez használható. Ezt az identitást használhatja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hozzárendeléseiben, amelyek lehetővé teszik az adatokhoz való hozzáférést az indexelés során.

![A rendszerhez rendelt felügyelt identitás bekapcsolása](./media/search-managed-identities/turn-on-system-assigned-identity.png "A rendszerhez rendelt felügyelt identitás bekapcsolása")

A **Mentés** gombra kattintva megjelenik egy objektumazonosító, amely hozzá van rendelve a keresési szolgáltatáshoz.

![Objektumazonosító](./media/search-managed-identities/system-assigned-identity-object-id.png "Objektumazonosító")
 
### <a name="2---add-a-role-assignment"></a>2 – szerepkör-hozzárendelés hozzáadása

Ebben a lépésben az Azure Cognitive Search-szolgáltatás engedélyt ad a Storage-fiók adatainak olvasásához.

1. A Azure Portal Navigáljon arra a Storage-fiókra, amely tartalmazza az indexelni kívánt adatkészletet.
2. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**

    ![Szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-storage.png "Szerepkör-hozzárendelés hozzáadása")

4. Válassza ki a megfelelő szerepkör (eke) t az indexelni kívánt Storage-fiók típusától függően:
    1. Az Azure Blob Storage használatához hozzá kell adnia a keresési szolgáltatást a **Storage blob Adatolvasó** szerepkörhöz.
    1. Azure Data Lake Storage Gen2 megköveteli a keresési szolgáltatás hozzáadását a **Storage blob Adatolvasó** szerepkörhöz.
    1. Az Azure Table Storage használatához hozzá kell adnia a keresési szolgáltatást az **olvasó és az adatelérési** szerepkörhöz.
5.  Az **Azure ad-felhasználó,-csoport vagy-szolgáltatásnév** **hozzáférésének** elhagyása
6.  Keresse meg a keresési szolgáltatást, jelölje ki, majd kattintson a **Mentés** gombra.

    Példa Azure Blob Storage-hoz és Azure Data Lake Storage Gen2:

    ![Storage blob adatolvasói szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "Storage blob adatolvasói szerepkör-hozzárendelés hozzáadása")

    Példa az Azure Table Storage szolgáltatásra:

    ![Olvasó-és adathozzáférési szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "Olvasó-és adathozzáférési szerepkör-hozzárendelés hozzáadása")

### <a name="3---create-the-data-source"></a>3 – az adatforrás létrehozása

A [REST API](/rest/api/searchservice/create-data-source), Azure Portal és a [.net SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) támogatja a felügyelt identitás-kapcsolatok karakterláncát. Az alábbi példa bemutatja, hogyan hozhat létre egy adatforrást egy Storage-fiók adatainak indexeléséhez a [REST API](/rest/api/searchservice/create-data-source) és egy felügyelt identitás-kapcsolódási karakterlánc használatával. A felügyelt identitás-kapcsolatok karakterlánc-formátuma megegyezik a REST API, a .NET SDK és a Azure Portal esetében.

Egy Storage-fiókból való indexeléskor az adatforrásnak a következő szükséges tulajdonságokkal kell rendelkeznie:

* a **Name** a keresési szolgáltatásban található adatforrás egyedi neve.
* **típusa**
    * Azure Blob Storage: `azureblob`
    * Azure Table Storage: `azuretable`
    * Azure Data Lake Storage Gen2: a **Type (típus) értéket adja** meg, amikor az [űrlapon](https://aka.ms/azure-cognitive-search/mi-preview-request)regisztrál az előzetes verzióra.
* **hitelesítő adatok**
    * Ha felügyelt identitást használ a hitelesítéshez, a **hitelesítő adatok** formátuma eltérő, mint ha nem használ felügyelt identitást. Itt meg kell adnia egy ResourceId, amely nem rendelkezik fiók kulcsával vagy jelszavával. A ResourceId tartalmaznia kell a Storage-fiók előfizetés-AZONOSÍTÓját, a Storage-fiók erőforráscsoporthoz, valamint a Storage-fiók nevét.
    * Felügyelt identitás formátuma: 
        * *ResourceId =/Subscriptions/**az előfizetés-azonosítóját****a/resourceGroups//Providers/Microsoft.Storage/storageAccounts/** a **Storage-fiók neve**/;*
* a tároló a Storage-fiókban **Megadja a** tároló vagy a tábla nevét. Alapértelmezés szerint a tárolóban lévő összes blob beolvasható. Ha a blobokat csak egy adott virtuális könyvtárban szeretné indexelni, megadhatja a könyvtárat a választható **lekérdezési** paraméter használatával.

Példa blob-adatforrás objektum létrehozására a [REST API](/rest/api/searchservice/create-data-source)használatával:

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 – az index létrehozása

Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit.

Ebből a témakörből megtudhatja, hogyan hozhat létre egy kereshető mezőt tartalmazó indexet `content` a blobokból kinyert szöveg tárolásához:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

További információk az indexek létrehozásáról: [create index](/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 – az indexelő létrehozása

Az indexelő összekapcsolja az adatforrást a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít.

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására.

Példa indexelő definíciója egy blob indexelő számára:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

Az indexelő API létrehozásával kapcsolatos további információkért tekintse meg az [Indexelő létrehozása](/rest/api/searchservice/create-indexer)című leírást.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

## <a name="accessing-secure-data-in-storage-accounts"></a>Biztonságos adatok elérése a Storage-fiókokban

Az Azure Storage-fiókokat tűzfalakkal és virtuális hálózatokkal is biztonságossá teheti. Ha egy blob Storage-fiókból Data Lake vagy egy tűzfallal vagy virtuális hálózattal védett Gen2 szeretne tartalmat indexelni, kövesse az [adatok biztonságos elérését a Storage-fiókokban a megbízható szolgáltatásokkal kapcsolatos kivételek segítségével](search-indexer-howto-access-trusted-service-exception.md).

## <a name="see-also"></a>Lásd még

További információ az Azure Storage indexelő szolgáltatásáról:

* [Azure Blob-indexelő](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 indexelő](search-howto-index-azure-data-lake-storage.md)
* [Azure Table indexelő](search-howto-indexing-azure-tables.md)