---
title: Az Azure szerepkör-definíciók ismertetése – Azure RBAC
description: Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Azure-beli szerepkör-definícióinak megismerése az Azure-erőforrások részletes hozzáférés-kezeléséhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 03/22/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: 5b2ec3289d187997763ee0d9280a777d4fa1f396
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801757"
---
# <a name="understand-azure-role-definitions"></a>Az Azure szerepkör-definíciók ismertetése

Ha meg szeretné ismerni, hogyan működik az Azure-szerepkör, vagy saját Azure-beli [Egyéni szerepkört](custom-roles.md)hoz létre, hasznos lehet megérteni a szerepkörök definiálásának módját. Ez a cikk a szerepkör-definíciók részleteit ismerteti, és példákat is tartalmaz.

## <a name="role-definition"></a>Szerepkör-definíció

A *szerepkör-definíció* engedélyek gyűjteménye. Ezt néha csak *szerepkörnek* nevezzük. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). Azokat a műveleteket is listázhatja, amelyek ki vannak zárva az engedélyezett műveletekről vagy az alapul szolgáló adatokkal kapcsolatos műveletekről.

Az alábbi példa a szerepkör-definíciók tulajdonságait mutatja be a Azure PowerShell használatával történő megjelenítéskor:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Az alábbi példa a szerepkör-definíciók tulajdonságait mutatja be, amikor a Azure Portal, az Azure CLI vagy a REST API használatával jelenik meg:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

A következő táblázat ismerteti, hogy a szerepkör tulajdonságai mit jelentenek.

| Tulajdonság | Leírás |
| --- | --- |
| `Name`</br>`roleName` | A szerepkör megjelenített neve. |
| `Id`</br>`name` | A szerepkör egyedi azonosítója. A beépített szerepkörök ugyanazzal a szerepkör-AZONOSÍTÓval rendelkeznek, mint a felhők között. |
| `IsCustom`</br>`roleType` | Azt jelzi, hogy ez egy egyéni szerepkör-e. `true` `CustomRole` Egyéni szerepkörökhöz vagy értékhez. `false` `BuiltInRole` Beépített szerepkörökhöz vagy értékhez. |
| `Description`</br>`description` | A szerepkör leírása. |
| `Actions`</br>`actions` | Karakterláncok tömbje, amely meghatározza, hogy a szerepkör milyen kezelési műveleteket hajtson végre. |
| `NotActions`</br>`notActions` | Karakterláncok tömbje, amely meghatározza az engedélyezetttől kizárt felügyeleti műveleteket `Actions` . |
| `DataActions`</br>`dataActions` | Karakterláncok tömbje, amely meghatározza azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. |
| `NotDataActions`</br>`notDataActions` | Karakterláncok tömbje, amely az engedélyezetttől kizárt adatműveleteket határozza meg `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | Karakterláncok tömbje, amely meghatározza azokat a hatóköröket, amelyekhez a szerepkör elérhető a hozzárendeléshez. |

### <a name="operations-format"></a>Működési formátum

A műveletek a következő formátumú karakterláncokkal vannak megadva:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A `{action}` műveleti karakterláncok része az erőforrástípus által elvégezhető műveletek típusát határozza meg. Például a következő alsztringek jelennek meg a ben `{action}` :

| Művelet alkarakterlánca    | Leírás         |
| ------------------- | ------------------- |
| `*` | A helyettesítő karakter a sztringnek megfelelő összes művelethez hozzáférést biztosít. |
| `read` | Olvasási műveletek (GET) engedélyezése. |
| `write` | Írási műveletek (PUT vagy javítás) engedélyezése. |
| `action` | Engedélyezi az olyan egyéni műveleteket, mint például a virtuális gépek újraindítása (POST). |
| `delete` | Engedélyezi a törlési műveleteket (Törlés). |

### <a name="role-definition-example"></a>Példa a szerepkör-definícióra

Itt látható a [közreműködő](built-in-roles.md#contributor) szerepkör definíciója Azure PowerShell és az Azure CLI-ben. Az `Actions``*` helyettesítő karakterének művelete azt jelzi, hogy a szerepkörhöz rendelt szolgáltatásnév minden műveletet elvégezhet, azaz mindent kezelhet. Ez az Azure által hozzáadott új erőforrástípusokkal együtt a jövőben meghatározott műveletekre is vonatkozik. A `NotActions` műveleteit kivonjuk az `Actions` elemből. A [közreműködő](built-in-roles.md#contributor) szerepkör esetében `NotActions` eltávolítja ezt a szerepkört az erőforrásokhoz való hozzáférés kezeléséhez és a Azure Blueprint-hozzárendelések kezeléséhez is.

Közreműködő szerepkör a Azure PowerShellban megjelenített módon:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Közreműködő szerepkör az Azure CLI-ben megjelenő módon:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Kezelési és adatműveletek

A felügyeleti műveletek szerepköralapú hozzáférés-vezérlése a `Actions` szerepkör-definíció és `NotActions` tulajdonságaiban van megadva. Íme néhány példa az Azure-beli felügyeleti műveletekre:

- Storage-fiókhoz való hozzáférés kezelése
- BLOB-tároló létrehozása, frissítése vagy törlése
- Erőforráscsoport és az összes erőforrás törlése

A felügyeleti hozzáférés nem örökli az adatait, ha a tároló hitelesítési módszere "Azure AD felhasználói fiók", és nem "hozzáférési kulcs". Ez az elkülönítés megakadályozza, hogy a helyettesítő karakterrel rendelkező szerepkörök ( `*` ) ne legyenek korlátozott hozzáférésük az adatokhoz. Ha például egy felhasználó rendelkezik egy előfizetéshez tartozó [olvasó](built-in-roles.md#reader) szerepkörrel, akkor megtekintheti a Storage-fiókot, de alapértelmezés szerint nem tekinthetik meg az alapul szolgáló adatokat.

Korábban a szerepköralapú hozzáférés-vezérlést nem használták adatműveletekhez. Az adatműveletek hitelesítése az erőforrás-szolgáltatókon keresztül változhat. A felügyeleti műveletekhez használt szerepköralapú hozzáférés-vezérlési hitelesítési modell kibővült az adatműveletekre.

Az adatműveletek támogatásához új Adattulajdonságok lettek hozzáadva a szerepkör-definícióhoz. Az adatműveletek a `DataActions` és `NotDataActions` tulajdonságokban mennek végbe. Ezen Adattulajdonságok hozzáadásával a rendszer megőrzi a felügyelet és az adatkezelés közötti elkülönítést. Az aktuális, helyettesítő karakterekkel (`*`) történő szerepkör-hozzárendelések így nem kaphatnak hirtelen hozzáférést az adatokhoz. A `DataActions` és `NotDataActions` elemekben többek között a következő adatműveletek adhatók meg:

- Egy tároló bloblistájának olvasása
- Tárolóblob írása egy tárolóban
- Egy üzenetsor üzenetének törlése

Itt látható a [Storage blob Adatolvasó](built-in-roles.md#storage-blob-data-reader) szerepkör-definíciója, amely a és a tulajdonságok között is tartalmaz műveleteket `Actions` `DataActions` . Ez a szerepkör lehetővé teszi a blob-tároló és a mögöttes blob-adat olvasását.

Storage blob-adatolvasói szerepkör a Azure PowerShellban megjelenő módon:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Storage blob-adatolvasói szerepkör az Azure CLI-ben megjelenő módon:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

A `DataActions` és `NotDataActions` tulajdonsághoz csak adatműveletek adhatók hozzá. Az erőforrás-szolgáltatók határozzák meg, hogy mely műveletek legyenek adatműveletek, ha a tulajdonságot értékre állítja `isDataAction` `true` . A következő műveletek listájának megtekintéséhez `isDataAction` `true` lásd: [erőforrás-szolgáltatói műveletek](resource-provider-operations.md). Azok a szerepkörök, amelyek nem rendelkeznek adatműveletekkel, nem szükségesek `DataActions` `NotDataActions` a szerepkör-definícióban lévő és a tulajdonságok eléréséhez.

Az összes felügyeleti művelet API-hívásának hitelesítését Azure Resource Manager kezeli. Az adatműveleti API-hívások engedélyezését vagy erőforrás-szolgáltató vagy Azure Resource Manager kezeli.

### <a name="data-operations-example"></a>Adatműveletek – példa

A kezelési és az adatműveletek működésének jobb megismeréséhez Vegyünk egy konkrét példát. Alice hozzá lett rendelve a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. Bob hozzárendelte a [Storage blob adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy Storage-fiók hatókörében. Az alábbi ábrán látható példa.

![A szerepköralapú hozzáférés-vezérlés ki van terjesztve a felügyeleti és az adatműveletek támogatására is](./media/role-definitions/rbac-management-data.png)

Az Alice [tulajdonosi](built-in-roles.md#owner) szerepköre és a [Storage blob adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepköre a következő műveletekkel rendelkezik:

Tulajdonos

&nbsp;&nbsp;&nbsp;&nbsp;Műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage-blobadatok közreműködője

&nbsp;&nbsp;&nbsp;&nbsp;Műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Mivel Alice egy helyettesítő karakter ( `*` ) művelettel rendelkezik egy előfizetési hatókörben, az engedélyeik öröklik az összes felügyeleti művelet végrehajtását. Alice képes a tárolók olvasására, írására és törlésére. Alice azonban nem végezheti el az adatműveleteket további lépések végrehajtása nélkül. Alapértelmezés szerint például Alice nem tudja beolvasni a blobokat egy tárolón belül. A Blobok olvasásához Alice-nek le kell kérnie a tároló-hozzáférési kulcsokat, és a Blobok eléréséhez használnia kell őket.

A Bob engedélyei a `Actions` `DataActions` [Storage blob-adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkörben csak a és a értékre korlátozódnak. A Bob szerepkör alapján a felügyeleti és az adatműveletek is elvégezhetők. Például Bob elolvashatja, írhatja és törölheti a tárolókat a megadott Storage-fiókban, valamint elolvashatja, írhatja és törölheti is a blobokat.

A tárolással kapcsolatos felügyelettel és adatsíkokkal kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Milyen eszközök támogatják az Azure-szerepkörök használatát az adatműveletekhez?

Az adatműveletek megtekintéséhez és használatához az eszközök vagy SDK-k megfelelő verzióját kell megadnia:

| Eszköz  | Verzió  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 vagy újabb |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 vagy újabb |
| [Azure .NET-hez](/dotnet/azure/) | 2.8.0 – előzetes verzió vagy újabb |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 vagy újabb |
| [Azure Java-hoz](/java/azure/) | 1.9.0 vagy újabb |
| [Azure Pythonhoz](/azure/python/) | 0.40.0 vagy újabb |
| [Rubyhoz készült Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 vagy újabb |

Az REST APIban lévő adatműveletek megtekintéséhez és használatához az **API-Version** paramétert az alábbi vagy újabb verzióra kell beállítani:

- 2018-07-01

## <a name="actions"></a>Műveletek

Az `Actions` engedély meghatározza azokat a felügyeleti műveleteket, amelyeket a szerepkör engedélyez. Ez a műveleti karakterláncok gyűjteménye, amelyek az Azure-erőforrás-szolgáltatók biztonságos műveleteit azonosítják. Íme néhány példa a szolgáltatásban használható felügyeleti műveletekre `Actions` .

> [!div class="mx-tableFixed"]
> | Műveleti sztring    | Leírás         |
> | ------------------- | ------------------- |
> | `*/read` | Olvasási műveletekhez való hozzáférést biztosít az összes Azure-erőforrás-szolgáltató összes erőforrás-típusához.|
> | `Microsoft.Compute/*` | Hozzáférést biztosít minden művelethez a Microsoft. számítási erőforrás-szolgáltató összes erőforrás-típusához.|
> | `Microsoft.Network/*/read` | Olvasási műveletekhez való hozzáférést biztosít a Microsoft. Network erőforrás-szolgáltató összes erőforrás-típusához.|
> | `Microsoft.Compute/virtualMachines/*` | Hozzáférést biztosít a virtuális gépek összes műveletéhez és annak alárendelt erőforrásaihoz.|
> | `microsoft.web/sites/restart/Action` | Hozzáférést biztosít egy webalkalmazás újraindításához.|

## <a name="notactions"></a>NotActions

Az `NotActions` engedély meghatározza azokat a felügyeleti műveleteket, amelyek ki vannak vonva vagy ki vannak zárva a `Actions` helyettesítő karakterrel ( `*` ). Akkor használja az `NotActions` engedélyt, ha az engedélyezni kívánt műveletek csoportja könnyebben definiálható, ha kivonja a `Actions` helyettesítő karaktert ( `*` ). A szerepkör (hatályos engedélyek) által biztosított hozzáférés kiszámítása a műveletek műveletből való kivonásával történik `NotActions` `Actions` .

`Actions - NotActions = Effective management permissions`

A következő táblázat két példát mutat be a [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement) helyettesítő karakteres műveletének hatályos engedélyeire:

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Hatályos felügyeleti engedélyek |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *nincs* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Ha a felhasználó olyan szerepkört rendel hozzá, amely kizár egy műveletet a alkalmazásban `NotActions` , és olyan második szerepkörhöz van rendelve, amely hozzáférést biztosít ugyanahhoz a művelethez, a felhasználó számára engedélyezett a művelet végrehajtása. `NotActions` nem megtagadási szabály – egyszerűen egy kényelmes módszer, amely lehetővé teszi, hogy az egyes műveleteket ki kell zárni.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>A nem Tapintatok és a megtagadási hozzárendelések közötti különbségek

`NotActions` a és a megtagadási hozzárendelések nem egyeznek meg, és különböző célokra szolgálnak. `NotActions` a helyettesítő karakteres () műveletből származó konkrét műveletek kivonásának kényelmes módja `*` .

A hozzárendelések megtagadása esetén a felhasználók akkor is elvégezhetnek konkrét műveleteket, ha a szerepkör-hozzárendelés hozzáférést biztosít számukra. További információ: az [Azure megtagadási hozzárendeléseinek megismerése](deny-assignments.md).

## <a name="dataactions"></a>DataActions

Az `DataActions` engedély meghatározza azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. Ha például egy felhasználó beolvasta a blob-adathozzáférést egy Storage-fiókhoz, akkor az adott Storage-fiókban található blobokat is elolvashatja. Íme néhány példa a szolgáltatásban használható adatműveletekre `DataActions` .

> [!div class="mx-tableFixed"]
> | Műveleti sztring    | Leírás         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Blobot vagy Blobok listáját adja vissza. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Egy blob írásának eredményét adja vissza. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Egy üzenetet ad vissza. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Egy üzenetet vagy egy üzenet írásának vagy törlésének eredményét adja vissza. |

## <a name="notdataactions"></a>NotDataActions

Az `NotDataActions` engedély meghatározza azokat az adatműveleteket, amelyek ki vannak vonva vagy ki vannak zárva a `DataActions` helyettesítő karakterrel ( `*` ). Akkor használja az `NotDataActions` engedélyt, ha az engedélyezni kívánt műveletek csoportja könnyebben definiálható, ha kivonja a `DataActions` helyettesítő karaktert ( `*` ). A szerepkör (hatályos engedélyek) által biztosított hozzáférés kiszámítása a műveletek műveletből való kivonásával történik `NotDataActions` `DataActions` . Mindegyik erőforrás-szolgáltató biztosítja az API-k megfelelő készletét az adatműveletek teljesítéséhez.

`DataActions - NotDataActions = Effective data permissions`

A következő táblázat két példát mutat be a [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) helyettesítő műveletének hatályos engedélyeire:

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | Érvényes adatengedélyek |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *nincs* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Ha egy felhasználó olyan szerepkört kap, amely kizár egy adatműveletet a alkalmazásban `NotDataActions` , és olyan második szerepkört rendel hozzá, amely ugyanahhoz az adatművelethez hozzáférést biztosít, akkor a felhasználó elvégezheti az adatműveletet. `NotDataActions` nem megtagadási szabály – egyszerűen csak egy kényelmes módszer, amely lehetővé teszi az adatműveletek egy készletének kizárását, ha bizonyos adatműveleteket ki kell zárni.
>

## <a name="assignablescopes"></a>AssignableScopes

A `AssignableScopes` tulajdonság határozza meg, hogy mely hatókörök (felügyeleti csoportok, előfizetések vagy erőforráscsoportok) érhetők el ehhez a szerepkör-definícióhoz. A szerepkört csak azokhoz a felügyeleti csoportokhoz, előfizetésekhez vagy erőforráscsoportokhöz lehet hozzárendelni, amelyekhez szükség van. Legalább egy felügyeleti csoportot, előfizetést vagy erőforráscsoportot kell használnia.

A beépített szerepkörök `AssignableScopes` a gyökérszintű hatókörre () vannak beállítva `"/"` . A gyökérszintű hatókör azt jelzi, hogy a szerepkör minden hatókörben elérhető a hozzárendeléshez. Érvényes hozzárendelhető hatókörök például a következők:

> [!div class="mx-tableFixed"]
> | A szerepkör hozzárendelésre elérhető | Példa |
> |----------|---------|
> | Egy előfizetés | `"/subscriptions/{subscriptionId1}"` |
> | Két előfizetés | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Hálózati erőforráscsoport | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Egy felügyeleti csoport | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Felügyeleti csoport és előfizetés | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Minden hatókör (csak a beépített szerepkörökre vonatkozik) | `"/"` |

További információ az `AssignableScopes` Egyéni szerepkörökről: [Egyéni Azure-szerepkörök](custom-roles.md).

## <a name="next-steps"></a>Következő lépések

* [Beépített Azure-szerepkörök](built-in-roles.md)
* [Egyéni Azure-szerepkörök](custom-roles.md)
* [Azure erőforrás-szolgáltatói műveletek](resource-provider-operations.md)
