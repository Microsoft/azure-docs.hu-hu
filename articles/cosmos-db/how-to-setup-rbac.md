---
title: Szerepköralapú hozzáférés-vezérlés konfigurálása a Azure Cosmos DB-fiókhoz az Azure AD-vel
description: Megtudhatja, hogyan konfigurálhat szerepköralapú hozzáférés-vezérlést a Azure Cosmos DB-fiókhoz Azure Active Directory
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/17/2021
ms.author: thweiss
ms.openlocfilehash: efde86eac3e0830b36eabfc9e80df09daeed9f6f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586063"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Szerepköralapú hozzáférés-vezérlés konfigurálása a Azure Cosmos DB-fiókhoz Azure Active Directoryhoz (előzetes verzió)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB szerepköralapú hozzáférés-vezérlés jelenleg előzetes verzióban érhető el. Ez az előzetes verzió szolgáltatói szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Ez a cikk a Azure Cosmos DB adatsíkjas műveleteinek szerepköralapú hozzáférés-vezérlését ismerteti. Ha felügyeleti sík-műveleteket használ, tekintse meg a felügyeleti sík műveleteire alkalmazott [szerepköralapú hozzáférés-vezérlés](role-based-access-control.md) című cikket.

A Azure Cosmos DB egy beépített szerepköralapú hozzáférés-vezérlési (RBAC) rendszert tesz elérhetővé, amely a következőket teszi lehetővé:

- Az adatkérések hitelesítése Azure Active Directory (Azure AD) identitással.
- A részletes, szerepköralapú engedélyezési modellel engedélyezheti az adatkéréseket.

## <a name="concepts"></a>Alapelvek

A Azure Cosmos DB adatsík RBAC olyan fogalmakra épül, amelyek más RBAC rendszerek, például az [Azure RBAC](../role-based-access-control/overview.md)esetében általában megtalálhatók:

- Az [engedélyezési modell](#permission-model) több **műveletből** áll; a műveletek mindegyike egy vagy több adatbázis-művelethez van leképezve. Néhány példa a műveletekre: elemek olvasása, elemek írása vagy lekérdezés végrehajtása.
- Azure Cosmos DB felhasználók az engedélyezett műveletek listáját tartalmazó **[szerepkör-definíciókat](#role-definitions)** hozhatnak létre.
- A szerepkör-definíciók meghatározott Azure AD-identitásokhoz vannak rendelve **[szerepkör-hozzárendeléseken](#role-assignments)** keresztül. A szerepkör-hozzárendelés meghatározza azt a hatókört is, amelyre a szerepkör-definíció vonatkozik; Jelenleg három hatókör van:
    - Egy Azure Cosmos DB fiók,
    - Egy Azure Cosmos DB-adatbázis,
    - Egy Azure Cosmos DB tároló.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC fogalmak":::

> [!NOTE]
> A Azure Cosmos DB RBAC jelenleg nem tesz elérhetővé beépített szerepkör-definíciókat.

## <a name="permission-model"></a><a id="permission-model"></a> Engedélyezési modell

Az alábbi táblázat felsorolja az engedélyezési modell által elérhető összes műveletet.

| Name | Megfelelő adatbázis-művelet (ek) |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Fiók metaadatainak olvasása. További részletekért lásd a [metaadat-kérelmeket](#metadata-requests) . |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Hozzon létre egy új elemet. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Az azonosító és a partíciós kulcs (pont – olvasás) alapján olvassa be az egyes elemeket. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Meglévő elemek cseréje |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" egy elem, ami azt jelenti, hogy nem létezik, vagy ha létezik, cserélje le. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Egy tétel törlése. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | SQL- [lekérdezés](sql-query-getting-started.md)végrehajtása. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Olvassa el a tároló [változási csatornáját](read-change-feed.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | [Tárolt eljárás](stored-procedures-triggers-udfs.md)végrehajtása. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | A többszörös írási régió fiókjainak [ütközései](conflict-resolution-policies.md) (azaz az ütköző hírcsatornából származó elemek listázása és törlése). |

A helyettesítő karakterek a *tárolók* és az *elemek* szintjein is támogatottak:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Ez az engedélyezési modell csak olyan adatbázis-műveletekre vonatkozik, amelyek lehetővé teszik az adatok olvasását és írását. **Nem** fedi le a felügyeleti műveleteket, például a tárolók létrehozását vagy az átviteli sebesség módosítását. A felügyeleti műveletek HRE-identitással történő hitelesítéséhez használja helyette az [Azure RBAC](role-based-access-control.md) .

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Metaadat-kérelmek

Azure Cosmos DB SDK-k használata esetén ezek az SDK-k az inicializáláskor és az adott adatkérések kiszolgálásához csak olvasási metaadatokat állítanak ki. Ezek a metaadat-kérelmek különböző konfigurációs adatokat olvasnak be, például: 

- A fiók globális konfigurációja, amely tartalmazza azokat az Azure-régiókat, amelyekben a fiók elérhető.
- A tárolók partíciós kulcsa vagy az indexelési szabályzat.
- A tárolót és azok címeit alkotó fizikai partíciók listája.

*Nem* kérik le a fiókjában tárolt összes adathalmazt.

Az engedélyezési modell lehető legjobb átláthatóságának biztosítása érdekében ezeket a metaadatokat a művelet kifejezetten szabályozza `Microsoft.DocumentDB/databaseAccounts/readMetadata` . Ezt a műveletet minden olyan helyzetben engedélyezni kell, amelyben a Azure Cosmos DB-fiókja a Azure Cosmos DB SDK-k egyikén keresztül érhető el. Hozzárendelhető (szerepkör-hozzárendeléssel) a Azure Cosmos DB hierarchia bármely szintjén (azaz fiók, adatbázis vagy tároló).

A művelet által engedélyezett tényleges metaadat `Microsoft.DocumentDB/databaseAccounts/readMetadata` -kérelmek attól a hatókörtől függenek, amelyre a művelet hozzá van rendelve:

| Hatókör | A művelet által engedélyezett kérelmek |
|---|---|
| Fiók | -A fiókban lévő adatbázisok listázása<br>– A fiók alatt lévő összes adatbázishoz az adatbázis hatókörében engedélyezett műveletek |
| Adatbázis | -Adatbázis metaadatainak olvasása<br>-Az adatbázishoz tartozó tárolók listázása<br>– Az adatbázis alatti összes tárolónál a tároló hatókörében engedélyezett műveletek |
| Tároló | -Tároló metaadatainak olvasása<br>-A tárolóban lévő fizikai partíciók listázása<br>– Az egyes fizikai partíciók címeinek feloldása |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Szerepkör-definíciók létrehozása

A szerepkör-definíciók létrehozásakor a következőket kell megadnia:

- A Azure Cosmos DB fiókjának neve.
- A fiókját tartalmazó erőforráscsoport.
- A szerepkör-definíció típusa; `CustomRole` jelenleg csak a támogatott.
- A szerepkör-definíció neve.
- Azoknak a [műveleteknek](#permission-model) a listája, amelyeknek engedélyezni szeretné a szerepkört.
- Egy vagy több olyan hatókör (ek), amelyhez a szerepkör-definíció társítható; a támogatott hatókörök a következők:
    - `/` (fiók szintű),
    - `/dbs/<database-name>` (adatbázis-szint),
    - `/dbs/<database-name>/colls/<container-name>` (Container-Level).

> [!NOTE]
> Az alább ismertetett műveletek jelenleg a következő címen érhetők el:
> - Azure PowerShell: [az. CosmosDB Version 2.0.1 – előzetes verzió](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: ["cosmosdb-Preview" bővítmény verziója 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Hozzon létre egy *MyReadOnlyRole* nevű szerepkört, amely csak olvasási műveleteket tartalmaz:

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

Hozzon létre egy *MyReadWriteRole* nevű szerepkört, amely az összes műveletet tartalmazza:

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

Az azonosítók beolvasásához létrehozott szerepkör-definíciók listázása:

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

Hozzon létre egy *MyReadOnlyRole* nevű szerepkört, amely csak olvasási műveleteket tartalmaz:

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

Hozzon létre egy *MyReadWriteRole* nevű szerepkört, amely az összes műveletet tartalmazza:

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

Az azonosítók beolvasásához létrehozott szerepkör-definíciók listázása:

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

A szerepkör-definíciók létrehozása után társíthatja őket a HRE-identitásokhoz. Szerepkör-hozzárendelés létrehozásakor meg kell adnia a következőket:

- A Azure Cosmos DB fiókjának neve.
- A fiókját tartalmazó erőforráscsoport.
- A hozzárendelni kívánt szerepkör-definíció azonosítója.
- Annak az identitásnak a fő azonosítója, amelyhez a szerepkör-definíciót hozzá kell rendelni.
- A szerepkör-hozzárendelés hatóköre; a támogatott hatókörök a következők:
    - `/` (fiók szintű)
    - `/dbs/<database-name>` (adatbázis szintű)
    - `/dbs/<database-name>/colls/<container-name>` (Container-Level)

  A hatókörnek egyeznie kell, vagy az egyik szerepkör-definíció hozzárendelhető hatókörének egy alhatókörének kell lennie.

> [!NOTE]
> Ha szerepkör-hozzárendelést szeretne létrehozni egy egyszerű szolgáltatáshoz, ügyeljen arra, hogy a **Azure Active Directory** portál panel **vállalati alkalmazások** szakaszában található **objektumazonosítót** használja.

> [!NOTE]
> Az alább ismertetett műveletek jelenleg a következő címen érhetők el:
> - Azure PowerShell: [az. CosmosDB Version 2.0.1 – előzetes verzió](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: ["cosmosdb-Preview" bővítmény verziója 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Szerepkör társítása identitáshoz:

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

Szerepkör társítása identitáshoz:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Az SDK inicializálása az Azure AD-vel

Ha a Azure Cosmos DB RBAC szeretné használni az alkalmazásban, frissítenie kell az Azure Cosmos DB SDK inicializálási módját. A fiók elsődleges kulcsának átadása helyett át kell adnia egy osztály egy példányát `TokenCredential` . Ez a példány biztosítja a Azure Cosmos DB SDK-t a használni kívánt identitás nevében egy HRE-jogkivonat beolvasásához szükséges környezettel.

A példány létrehozásának módja `TokenCredential` meghaladja a jelen cikk hatókörét. Számos módon hozhat létre ilyen példányt a használni kívánt HRE-identitás típusától függően (felhasználói tag, egyszerű szolgáltatásnév, csoport stb.). Ami a legfontosabb, hogy a `TokenCredential` példánynak fel kell oldania az identitást (a résztvevő azonosítóját), amelyhez hozzárendelte a szerepköröket. Példákat talál egy `TokenCredential` osztály létrehozására:

- [a .NET-ben](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [Java-ban](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)
- [a JavaScriptben](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme#credential-classes)

Az alábbi példák egy egyszerű szolgáltatást használnak egy `ClientSecretCredential` példánnyal.

### <a name="in-net"></a>A .NET-ben

Az Azure Cosmos DB RBAC jelenleg a `preview` [.net SDK v3](sql-api-sdk-dotnet-standard.md)verziója támogatja.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>A Javában

A Azure Cosmos DB RBAC jelenleg támogatott a [Java SDK v4](sql-api-sdk-java-v4.md)-ben.

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

### <a name="in-javascript"></a>A JavaScriptben

A Azure Cosmos DB RBAC jelenleg a [JavaScript SDK v3](sql-api-sdk-node.md)verzióban támogatott.

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

A Azure Cosmos DB RBAC használatakor a [diagnosztikai naplók](cosmosdb-monitor-resource-logs.md) kibővülnek az egyes adatműveletek identitás-és engedélyezési adataival. Így részletes naplózást hajthat végre, és lekérheti a Azure Cosmos DB-fiókjába eljuttatott minden adatkéréshez használt HRE-identitást.

Ez a további információ a **DataPlaneRequests** -napló kategóriájában folyik, és két további oszlopból áll:

- `aadPrincipalId_g` Megjeleníti a kérelem hitelesítéséhez használt HRE-identitás résztvevő-AZONOSÍTÓját.
- `aadAppliedRoleAssignmentId_g` Megjeleníti a kérés engedélyezésekor tiszteletben lévő [szerepkör-hozzárendelést](#role-assignments) .

## <a name="limits"></a>Korlátok

- Azure Cosmos DB-fiókkal akár 100 szerepkör-definíciót és 2 000 szerepkör-hozzárendelést is létrehozhat.
- Az Azure AD-csoport feloldása jelenleg nem támogatott olyan identitások esetén, amelyek több mint 200 csoporthoz tartoznak.
- Az Azure AD-jogkivonatot a rendszer jelenleg a Azure Cosmos DB szolgáltatásnak küldött egyes kérések fejlécében adja át, ami növeli a hasznos adatok teljes méretét.
- Az adatok Azure AD-vel való elérése a [Azure Cosmos db Explorerben](data-explorer.md) még nem támogatott. A Azure Cosmos DB Explorerrel továbbra is szükség van arra, hogy a felhasználó most hozzáférhessen a fiók elsődleges kulcsához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Mely Azure Cosmos DB API-kat támogatja az RBAC?

Jelenleg csak az SQL API támogatott.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Elvégezhető a szerepkör-definíciók és szerepkör-hozzárendelések kezelése az Azure Portalon?

A szerepkörkezelés az Azure Portalon még nem érhető el.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Mely SDK-k támogatják a Azure Cosmos DB SQL API-t? RBAC?

A [.net v3](sql-api-sdk-dotnet-standard.md) és a [Java v4](sql-api-sdk-java-v4.md) SDK-k jelenleg támogatottak.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Az Azure Cosmos DB SDK-i automatikusan frissítik az Azure AD-jogkivonatot, amikor az lejár?

Igen.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Letiltható a fiók elsődleges kulcsának használata az RBAC esetén?

A fiók elsődleges kulcsának letiltása jelenleg nem lehetséges.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Cosmos db lévő adathozzáférések biztonságos elérését](secure-access-to-data.md)ismertető cikket.
- További információ a [Azure Cosmos db felügyeletének RBAC](role-based-access-control.md).
