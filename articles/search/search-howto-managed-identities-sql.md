---
title: Azure SQL Database-kapcsolatok beállítása felügyelt identitás használatával
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan állíthat be egy indexelő-kapcsolódást Azure SQL Database felügyelt identitás használatával
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358422"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Indexelő-kapcsolatok beállítása Azure SQL Database felügyelt identitás használatával

Ez a lap azt ismerteti, hogyan állítható be egy indexelő-kapcsolódás a Azure SQL Database felügyelt identitás használatával, nem biztosítva hitelesítő adatokat az adatforrás-objektum kapcsolódási karakterláncában.

Mielőtt többet szeretne megtudni a szolgáltatásról, javasoljuk, hogy Ismerje meg, mi az indexelő, és hogyan állítható be egy indexelő az adatforráshoz. További információt az alábbi hivatkozásokon talál:

* [Az indexelő áttekintése](search-indexer-overview.md)
* [Azure SQL-indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>A kapcsolatok beállítása felügyelt identitás használatával

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 – a rendszerhez rendelt felügyelt identitás bekapcsolása

Ha egy rendszerhez rendelt felügyelt identitás engedélyezve van, az Azure létrehoz egy identitást a keresési szolgáltatáshoz, amely az ugyanazon a bérlőn és előfizetésen belüli más Azure-szolgáltatásokban való hitelesítéshez használható. Ezt az identitást használhatja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hozzárendeléseiben, amelyek lehetővé teszik az adatokhoz való hozzáférést az indexelés során.

![A rendszerhez rendelt felügyelt identitás bekapcsolása](./media/search-managed-identities/turn-on-system-assigned-identity.png "A rendszerhez rendelt felügyelt identitás bekapcsolása")

A **Mentés** gombra kattintva megjelenik egy objektumazonosító, amely hozzá van rendelve a keresési szolgáltatáshoz.

![Objektumazonosító](./media/search-managed-identities/system-assigned-identity-object-id.png "Objektumazonosító")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 – Azure Active Directory rendszergazda kiépítése SQL Server számára

Ha a következő lépésben csatlakozik az adatbázishoz, csatlakoznia kell egy olyan Azure Active Directory (Azure AD) fiókhoz, amely rendszergazdai hozzáféréssel rendelkezik az adatbázishoz ahhoz, hogy a keresési szolgáltatás hozzáférhessen az adatbázishoz.

Kövesse az [itt](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) található utasításokat, és adja meg az Azure ad-fiókja rendszergazdai hozzáférését az adatbázishoz.

### <a name="3---assign-the-search-service-permissions"></a>3 – a keresési szolgáltatás engedélyeinek társítása

Az alábbi lépések végrehajtásával rendelje hozzá a keresési szolgáltatás engedélyét az adatbázis olvasásához.

1. Kapcsolódás a Visual studióhoz

    ![Kapcsolódás a Visual studióhoz](./media/search-managed-identities/connect-with-visual-studio.png "Kapcsolódás a Visual studióhoz")

2. Hitelesítés az Azure AD-fiókkal

    ![Hitelesítés](./media/search-managed-identities/visual-studio-authentication.png "Hitelesítés")

3. Hajtsa végre a következő parancsokat:

    Adja meg a zárójeleket a keresési szolgáltatás neve körül.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Új lekérdezés](./media/search-managed-identities/visual-studio-new-query.png "Új lekérdezés")

    ![Lekérdezés végrehajtása](./media/search-managed-identities/visual-studio-execute-query.png "Lekérdezés végrehajtása")

>[!NOTE]
> Ha az 1. lépésben szereplő keresési szolgáltatás identitása a lépés befejezése után módosul, akkor el kell távolítania a szerepkör-tagságot, és el kell távolítania a felhasználót az SQL-adatbázisban, majd újra hozzá kell adnia az engedélyeket a 3. lépés ismételt végrehajtásával.
> A szerepkör-tagság és a felhasználó eltávolítása a következő parancsok futtatásával végezhető el:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 – szerepkör-hozzárendelés hozzáadása

Ebben a lépésben az Azure Cognitive Search-szolgáltatás engedélyt ad az adatok olvasására a SQL Server.

1. A Azure Portal navigáljon az Azure SQL Server oldalára.
2. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**

    ![Szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-sql-server.png "Szerepkör-hozzárendelés hozzáadása")

4. Válassza ki a megfelelő **olvasó** szerepkört.
5. Az **Azure ad-felhasználó,-csoport vagy-szolgáltatásnév** **hozzáférésének** elhagyása
6. Keresse meg a keresési szolgáltatást, jelölje ki, majd kattintson a **Mentés** gombra.

    ![Olvasói szerepkör-hozzárendelés hozzáadása](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Olvasói szerepkör-hozzárendelés hozzáadása")

### <a name="5---create-the-data-source"></a>5 – az adatforrás létrehozása

A [REST API](/rest/api/searchservice/create-data-source), Azure Portal és a [.net SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) támogatja a felügyelt identitás-kapcsolatok karakterláncát. Az alábbi példa bemutatja, hogyan hozhat létre egy adatforrást egy Azure SQL Database adatainak indexeléséhez a [REST API](/rest/api/searchservice/create-data-source) és egy felügyelt identitás-kapcsolódási karakterlánc használatával. A felügyelt identitás-kapcsolatok karakterlánc-formátuma megegyezik a REST API, a .NET SDK és a Azure Portal esetében.

Amikor a [REST API](/rest/api/searchservice/create-data-source)használatával hoz létre adatforrást, az adatforrásnak a következő szükséges tulajdonságokkal kell rendelkeznie:

* a **Name** a keresési szolgáltatásban található adatforrás egyedi neve.
* **típus**`azuresql`
* **hitelesítő adatok**
    * Ha felügyelt identitást használ a hitelesítéshez, a **hitelesítő adatok** formátuma különbözik, mint ha nem használ egy összekeveredéses identitást. Itt meg kell adnia egy kezdeti katalógus vagy adatbázis nevét, valamint egy olyan ResourceId, amely nem rendelkezik fiók-kulccsal vagy jelszóval. A ResourceId tartalmaznia kell a Azure SQL Database előfizetés-AZONOSÍTÓját, SQL Database erőforrás-csoportját és az SQL-adatbázis nevét. 
    * Felügyelt identitás-kapcsolatok karakterláncának formátuma:
        * *Kezdeti katalógus | Adatbázis =**adatbázis neve**; ResourceId =/Subscriptions/**az előfizetés-azonosítóját****az/resourceGroups//Providers/Microsoft.SQL/Servers/** a **SQL Server neve**/; Kapcsolat időtúllépése = a **kapcsolat időtúllépési hossza**;*
* a **Container (tároló** ) megadja az indexelni kívánt tábla vagy nézet nevét.

Példa Azure SQL adatforrás-objektum létrehozására a [REST API](/rest/api/searchservice/create-data-source)használatával:

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 – az index létrehozása

Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit.

Ebből a témakörből megtudhatja, hogyan hozhat létre egy indexet egy kereshető `booktitle` mezővel:   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

További információk az indexek létrehozásáról: [create index](/rest/api/searchservice/create-index)

### <a name="7---create-the-indexer"></a>7 – az indexelő létrehozása

Az indexelő összekapcsolja az adatforrást a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít.

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására.

Példa indexelő definíciója egy Azure SQL-indexelő esetében:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

Az indexelő API létrehozásával kapcsolatos további információkért tekintse meg az [Indexelő létrehozása](/rest/api/searchservice/create-indexer)című leírást.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha hibaüzenet jelenik meg, amikor az indexelő megpróbál csatlakozni az adatforráshoz, amely szerint az ügyfél nem fér hozzá a kiszolgálóhoz, vessen egy pillantást a [gyakori indexelő hibákra](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Lásd még

További információ az Azure SQL indexelő szolgáltatásról:
* [Azure SQL-indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)