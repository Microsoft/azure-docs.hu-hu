---
title: Szerepköralapú hozzáférés-vezérlés konfigurálása a Azure Cosmos DB-fiókhoz az Azure AD-val
description: Megtudhatja, hogyan konfigurálhatja a szerepköralapú hozzáférés-vezérlést Azure Active Directory a Azure Cosmos DB fiókjához
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/19/2021
ms.author: thweiss
ms.openlocfilehash: 209d18dfbadea89f14fd90da9a1bc57b3ccf0dfe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728072"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Szerepköralapú hozzáférés-vezérlés konfigurálása Azure Active Directory fiókhoz Azure Cosmos DB (előzetes verzió)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB szerepköralapú hozzáférés-vezérlés jelenleg előzetes verzióban érhető el. Ez az előzetes verzió nem szolgáltatói szerződés, és nem ajánlott éles számítási feladatokhoz. További információ: Kiegészítő használati feltételek a Microsoft Azure [előzetes verziókhoz.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!NOTE]
> Ez a cikk az adatsík-műveletek szerepköralapú hozzáférés-vezérlését Azure Cosmos DB. Ha felügyeleti síkon alapuló műveleteket használ, tekintse meg a felügyeleti sík műveleteire alkalmazott szerepköralapú [hozzáférés-vezérlést.](role-based-access-control.md)

Azure Cosmos DB elérhetővé teszi a beépített szerepköralapú hozzáférés-vezérlési (RBAC) rendszert, amely a következőt teszi lehetővé:

- Hitelesítheti az adatkéréseket egy Azure Active Directory (Azure AD) identitással.
- Engedélyezze az adatkéréseket egy finomhangolt, szerepköralapú engedélymodellel.

## <a name="concepts"></a>Alapelvek

A Azure Cosmos DB RBAC olyan fogalmakra épül, amelyek más RBAC-rendszerekben, például az [Azure RBAC-ben gyakoriak:](../role-based-access-control/overview.md)

- Az [engedélymodell](#permission-model) egy műveletkészletből **áll;** ezek a műveletek egy vagy több adatbázis-művelethez vannak leképezve. Néhány művelet lehet például egy elem olvasása, egy elem írása vagy egy lekérdezés végrehajtása.
- Azure Cosmos DB felhasználók **[az](#role-definitions)** engedélyezett műveletek listáját tartalmazó szerepkör-definíciókat hoznak létre.
- A szerepkör-definíciók adott Azure AD-identitásokhoz vannak hozzárendelve **[szerepkör-hozzárendelésekkel.](#role-assignments)** A szerepkör-hozzárendelés azt a hatókört is meghatározza, amelyre a szerepkör-definíció vonatkozik; jelenleg három hatókör van:
    - Egy Azure Cosmos DB fiók,
    - Egy Azure Cosmos DB adatbázis,
    - Egy Azure Cosmos DB tároló.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC-fogalmak":::

> [!NOTE]
> A Azure Cosmos DB RBAC jelenleg nem tesz elérhetővé beépített szerepkör-definíciókat.

## <a name="permission-model"></a><a id="permission-model"></a> Engedélymodell

> [!IMPORTANT]
> Ez az engedélymodell csak azokat az adatbázis-műveleteket fedi le, amelyek segítségével adatokat olvashat és írhat. Nem fed **le semmilyen** felügyeleti műveletet, például tárolók létrehozását vagy az átviteli sebességük megváltoztatását. Ez azt jelenti, hogy nem használhat egyetlen Azure Cosmos DB **SDK-t** sem a felügyeleti műveletek AAD-identitással való hitelesítéséhez. Ehelyett az [Azure RBAC-t a következőn keresztül kell](role-based-access-control.md) használnia:
> - [ARM-sablonok](manage-with-templates.md)
> - [Azure PowerShell szkriptek ,](manage-with-powershell.md)
> - [Azure CLI-szkriptek](manage-with-cli.md),
> - Az Azure felügyeleti kódtárai a következőben érhetők el:
>   - [.NET](https://www.nuget.org/packages/Azure.ResourceManager.CosmosDB)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)

Az alábbi táblázat felsorolja az engedélymodell által elérhetővé tett összes műveletet.

| Name | Megfelelő adatbázis-műveletek |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Fiók metaadatainak olvasása. A [részleteket lásd: Metaadat-kérelmek.](#metadata-requests) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Hozzon létre egy új elemet. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Egy adott elem olvasása az azonosító és a partíciókulcs (point-read) alapján. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Meglévő elem cseréje. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" egy elemet, ami azt jelenti, hogy ha még nem létezik, hozza létre, vagy cserélje le, ha létezik. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Elem törlése. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | [Sql-lekérdezés végrehajtása.](sql-query-getting-started.md) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Olvassa el a tároló [változáscsatornáját.](read-change-feed.md) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Hajtson [végre egy tárolt eljárást.](stored-procedures-triggers-udfs.md) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Kezelheti [a több](conflict-resolution-policies.md) írási régióból álló fiókok ütközését (például listába sorolja és törölje az elemeket az ütközéscsatornából). |

A helyettesítő karakterek tároló- és *elemszinten* is *támogatottak:*

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Metaadat-kérelmek

Az Azure Cosmos DB-k használata esetén ezek az SDK-k csak olvasható metaadat-kéréseket adnak ki az inicializálás során, és adott adatkérések kiszolgálására szolgálnak. Ezek a metaadat-kérések különböző konfigurációs adatokat kérnek le, például: 

- A fiók globális konfigurációja, amely tartalmazza azokat az Azure-régiókat, amelyekben a fiók elérhető.
- A tárolók partíciókulcsa vagy azok indexelési szabályzata.
- A tárolót és azok címét tartalmazó fizikai partíciók listája.

Nem *tudják lekérni* a fiókjában tárolt adatokat.

Az engedélymodell lehető legjobb átláthatóságának biztosítása érdekében a művelet kifejezetten lefedi ezeket a `Microsoft.DocumentDB/databaseAccounts/readMetadata` metaadat-kéréseket. Ezt a műveletet minden olyan esetben érdemes lehetővé tenni, amikor Azure Cosmos DB fiók az egyik Azure Cosmos DB érhető el. Hozzárendelhető (szerepkör-hozzárendelésen keresztül) a Azure Cosmos DB (vagyis fiók, adatbázis vagy tároló) bármely szintjén.

A művelet által engedélyezett tényleges metaadat-kérelmek attól függnek, hogy a művelet `Microsoft.DocumentDB/databaseAccounts/readMetadata` milyen hatókörhöz van hozzárendelve:

| Hatókör | A művelet által engedélyezett kérelmek |
|---|---|
| Fiók | – A fiókhoz sorolja fel az adatbázisokat<br>– A fiókhoz tartozó összes adatbázishoz az adatbázis hatókörében engedélyezett műveletek tartoznak |
| Adatbázis | - Adatbázis-metaadatok olvasása<br>– Az adatbázis alatt található tárolók listázása<br>– Az adatbázis minden egyes tárolója számára a tároló hatókörében engedélyezett műveletek |
| Tároló | - Tároló metaadatainak olvasása<br>– Fizikai partíciók listázása a tároló alatt<br>- Az egyes fizikai partíciók címének feloldása |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Szerepkör-definíciók létrehozása

Szerepkör-definíció létrehozásakor meg kell adnia a következő adatokat:

- A fiók Azure Cosmos DB neve.
- A fiókját tartalmazó erőforráscsoport.
- A szerepkör-definíció típusa; A `CustomRole` jelenleg csak a következőt támogatja: .
- A szerepkör-definíció neve.
- A szerepkör [által](#permission-model) engedélyezni kívánt műveletek listája.
- Egy vagy több hatókör, amely(ök)hez a szerepkör-definíció hozzárendelhető; támogatott hatókörök:
    - `/` (fiókszintű),
    - `/dbs/<database-name>` (adatbázisszintű),
    - `/dbs/<database-name>/colls/<container-name>` (tárolószintű).

> [!NOTE]
> Az alábbiakban ismertetett műveletek jelenleg a következőben érhetők el:
> - Azure PowerShell: [Az.CosmosDB 2.0.1-preview verziója](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [cosmosdb-preview bővítmény 0.4.0-s verzió](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Hozzon létre egy *MyReadOnlyRole nevű szerepkört,* amely csak olvasási műveleteket tartalmaz:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Hozzon létre egy *MyReadWriteRole nevű szerepkört,* amely az összes műveletet tartalmazza:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Sorolja fel azokat a szerepkör-definíciókat, amelyek az ön által létrehozott, a sajátjukhoz való beolvasásához vannak létrehozva:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Hozzon létre egy *MyReadOnlyRole nevű szerepkört,* amely csak olvasási műveleteket tartalmaz:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Hozzon létre egy *MyReadWriteRole nevű szerepkört,* amely az összes műveletet tartalmazza:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Sorolja fel azokat a szerepkör-definíciókat, amelyek az ön által létrehozott, a sajátjukhoz való beolvasásához vannak létrehozva:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Szerepkör-hozzárendelések létrehozása

Miután létrehozta a szerepkör-definíciókat, társíthatja őket az AAD-identitásokkal. Szerepkör-hozzárendelés létrehozásakor meg kell adnia a következő adatokat:

- A fiók Azure Cosmos DB neve.
- A fiókját tartalmazó erőforráscsoport.
- A hozzárendelni fog szerepkör-definíció azonosítója.
- Annak az identitásnak az egyszerű azonosítója, amelyhez a szerepkör-definíciót hozzá kell rendelni.
- A szerepkör-hozzárendelés hatóköre; támogatott hatókörök:
    - `/` (fiókszintű)
    - `/dbs/<database-name>` (adatbázisszintű)
    - `/dbs/<database-name>/colls/<container-name>` (tárolószintű)

  A hatókörnek egyeznie kell, vagy a szerepkör-definíció egyik hozzárendelhető hatókörének alhatókörének kell lennie.

> [!NOTE]
> Ha szerepkör-hozzárendelést szeretne létrehozni egy szolgáltatásnévhez,  ügyeljen arra, hogy  az objektumazonosítóját használja a portál panel Vállalati alkalmazások **Azure Active Directory** szakaszában.

> [!NOTE]
> Az alábbiakban ismertetett műveletek jelenleg a következőben érhetők el:
> - Azure PowerShell: [Az.CosmosDB 2.0.1-preview verziója](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [cosmosdb-preview bővítmény 0.4.0-s verzió](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Szerepkör hozzárendelése egy identitáshoz:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Szerepkör hozzárendelése identitáshoz:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Az SDK inicializálása az Azure AD-val

Ahhoz, hogy a Azure Cosmos DB RBAC-t az alkalmazásban használva tudja használni, frissítenie kell a Azure Cosmos DB SDK inicializálását. A fiók elsődleges kulcsának átadása helyett át kell adni egy osztály egy `TokenCredential` példányát. Ez a példány biztosítja Azure Cosmos DB SDK-t a használni kívánt identitás nevében az AAD-jogkivonat lekéréséhez szükséges környezethez.

A példányok létrehozási módja túlmutat ennek `TokenCredential` a cikknek a hatókörében. Egy ilyen példányt számos módon létrehozhat a használni kívánt AAD-identitás típusától függően (egyszerű felhasználó, szolgáltatásnév, csoport stb.). A legfontosabb, hogy a példánynak arra az identitásra (rendszerbiztonsági tagazonosítóra) kell feloldva lennie, `TokenCredential` amelyhez a szerepköröket rendelte. Példákat találhat egy osztály `TokenCredential` létrehozására:

- [.NET-en](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java nyelven](/java/api/overview/azure/identity-readme#credential-classes)
- [JavaScriptben](/javascript/api/overview/azure/identity-readme#credential-classes)

Az alábbi példák egyszerű szolgáltatást használnak a `ClientSecretCredential` példányokkal.

### <a name="in-net"></a>A .NET-en

A Azure Cosmos DB RBAC jelenleg a `preview` [.NET SDK V3 verziójában támogatott.](sql-api-sdk-dotnet-standard.md)

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>A Javában

A Azure Cosmos DB RBAC jelenleg a [Java SDK V4-ben támogatott.](sql-api-sdk-java-v4.md)

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>JavaScriptben

A Azure Cosmos DB RBAC jelenleg a [JavaScript SDK V3-ban támogatott.](sql-api-sdk-node.md)

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="auditing-data-requests"></a>Adatkérések naplózása

Az RBAC Azure Cosmos DB diagnosztikai naplók identitás- és engedélyezési információkkal egészülnek ki az egyes adatműveletekkel kapcsolatban. [](cosmosdb-monitor-resource-logs.md) Ez lehetővé teszi a részletes naplózást, és lekérheti a fióknak küldött minden adatkéréshez használt AAD-Azure Cosmos DB lekérését.

Ezek a további információk a **DataPlaneRequests** naplókategóriába tartoznak, és két további oszlopból áll:

- `aadPrincipalId_g` A a kérés hitelesítéséhez használt AAD-identitás egyszerű azonosítóját jeleníti meg.
- `aadAppliedRoleAssignmentId_g` A azt [a szerepkör-hozzárendelést](#role-assignments) jeleníti meg, amely a kérelem érvénybe való igénylésekor lett tiszteletben véve.

## <a name="limits"></a>Korlátok

- Fiókonként legfeljebb 100 szerepkör-definíciót és 2000 szerepkör-Azure Cosmos DB hozhat létre.
- Szerepkör-definíciókat csak olyan Azure AD-identitásokhoz rendelhet hozzá, amelyek ugyanakhoz az Azure AD-bérlőhöz tartoznak, mint a Azure Cosmos DB fiókja.
- Az Azure AD-csoportfeloldás jelenleg nem támogatott a több mint 200 csoporthoz tartozó identitások esetében.
- Az Azure AD-jogkivonat jelenleg fejlécként van átküldve, és minden egyes kérést elküld a Azure Cosmos DB szolgáltatásnak, ami növeli a hasznos kódot.
- Az adatok Azure AD-val való elérése [Azure Cosmos DB Explorer](data-explorer.md) még nem támogatott. A Azure Cosmos DB Explorer használatához a felhasználónak most is hozzá kell férni a fiók elsődleges kulcsához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Mely Azure Cosmos DB API-kat támogatja az RBAC?

Jelenleg csak az SQL API támogatott.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Elvégezhető a szerepkör-definíciók és szerepkör-hozzárendelések kezelése az Azure Portalon?

A szerepkörkezelés az Azure Portalon még nem érhető el.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Az SQL API mely AZURE COSMOS DB támogatja az RBAC-t?

A [.NET V3](sql-api-sdk-dotnet-standard.md) és [a Java V4](sql-api-sdk-java-v4.md) SDK-k jelenleg támogatottak.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Az Azure Cosmos DB SDK-i automatikusan frissítik az Azure AD-jogkivonatot, amikor az lejár?

Igen.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Letiltható a fiók elsődleges kulcsának használata az RBAC esetén?

A fiók elsődleges kulcsának letiltása jelenleg nem lehetséges.

## <a name="next-steps"></a>Következő lépések

- Az adatokhoz való biztonságos [hozzáférés áttekintése](secure-access-to-data.md)a Cosmos DB.
- További információ a [felügyelethez Azure Cosmos DB RBAC-ről.](role-based-access-control.md)