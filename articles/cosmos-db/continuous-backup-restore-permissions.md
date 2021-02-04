---
title: Azure Cosmos DB-fiók visszaállítására vonatkozó engedélyek konfigurálása.
description: Megtudhatja, hogyan különítheti el és korlátozhatja a folyamatos biztonsági mentési fiókra vonatkozó visszaállítási engedélyeket egy adott szerepkör vagy rendszerbiztonsági tag számára. Bemutatja, hogyan rendelhet hozzá egy beépített szerepkört Azure Portal, parancssori felület vagy egyéni szerepkör definiálásával.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9d30f5325162b9ea447d54aadc092dbd9aa29132
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538693"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók visszaállítására vonatkozó engedélyek kezelése
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB lehetővé teszi a folyamatos biztonsági mentési (előzetes) fiókra vonatkozó visszaállítási engedélyek elkülönítését és korlátozását egy adott szerepkör vagy rendszerbiztonsági tag számára. A fiók tulajdonosa aktiválhatja a visszaállítást, és hozzárendelhet egy szerepkört a többi résztvevőhöz a visszaállítási művelet elvégzéséhez. Ezek az engedélyek az előfizetés hatókörén vagy részletesebben alkalmazhatók a forrásoldali fiók hatókörében, ahogy az alábbi képen is látható:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="A visszaállítási művelet végrehajtásához szükséges szerepkörök listája." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

A hatókör olyan erőforrások készlete, amelyeknek hozzáférése van, a hatókörökkel kapcsolatos további információkért tekintse meg az [Azure RBAC](../role-based-access-control/scope-overview.md) dokumentációját. Azure Cosmos DB a használati esetek többségében a megfelelő hatókörök a forrás-előfizetés és az adatbázis-fiók. A visszaállítási műveleteket végző rendszerbiztonsági csoportnak írási engedéllyel kell rendelkeznie a cél erőforráscsoporthoz.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Szerepkörök társítása visszaállításhoz a Azure Portal használatával

A visszaállításhoz a felhasználónak vagy a rendszerbiztonsági tag engedélyre van szüksége a visszaállításhoz (ez a "visszaállítás/művelet" engedély), és engedélyt ad egy új fiók kiépítésére (azaz "írás" engedélyre).  Ezen engedélyek megadásához a tulajdonos hozzárendelheti az "CosmosRestoreOperator" és a "Cosmos DB operátor" szerepkört a rendszerbiztonsági tag számára.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)

1. Lépjen az előfizetéshez, és lépjen a **hozzáférés-vezérlés (iam)** lapra, **és válassza a**  >  **szerepkör-hozzárendelés hozzáadása** elemet.

1. A **szerepkör-hozzárendelés hozzáadása** panelen, a **szerepkör** mezőben válassza a **CosmosRestoreOperator** szerepkör lehetőséget. Válassza a **felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget a **hozzáférés kiosztása** mezőhöz, és keresse meg a felhasználó nevét vagy e-mail-azonosítóját az alábbi képen látható módon:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="CosmosRestoreOperator és Cosmos DB operátori szerepköröket rendelhet hozzá." border="true":::

1. Válassza a **Mentés** lehetőséget a "visszaállítás/művelet" engedély megadásához.

1. Ismételje meg a 3. lépést **Cosmos db operátori** szerepkörrel az írási engedély megadásához. Ha ezt a szerepkört a Azure Portal-ból rendeli hozzá, a teljes előfizetéshez engedélyezi a visszaállítási engedélyt.

## <a name="permission-scopes"></a>Engedélyek hatókörei

|Hatókör  |Példa  |
|---------|---------|
|Előfizetés | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Erőforráscsoport | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB helyreállítható fiók erőforrása | /Subscriptions/00000000-0000-0000-0000-000000000000/Providers/Microsoft.DocumentDB/Locations/USA nyugati régiója/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

A helyreállítható fiók erőforrás a `az cosmosdb restorable-database-account list --name <accountname>` parancssori felületen vagy a `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` PowerShell-parancsmagban található parancs kimenetében is kinyerhető. A kimenet Name attribútuma a helyreállítható fiók "instanceID" jelöli. További információt a [PowerShell](continuous-backup-restore-powershell.md) vagy a [parancssori](continuous-backup-restore-command-line.md) felület című cikkben talál.

## <a name="permissions"></a>Engedélyek

A következő engedélyek szükségesek a folyamatos biztonsági mentési módú fiókok visszaállítására vonatkozó különböző tevékenységek végrehajtásához:

|Engedély  |Hatás  |Minimális hatókör  |Maximális hatókör  |
|---------|---------|---------|---------|
|Microsoft. Resources/Deployments/validate/Action, Microsoft. Resources/üzemelő példány/írás | Ezek az engedélyek szükségesek ahhoz, hogy az ARM-sablon üzembe kerüljön a visszaállított fiók létrehozásához. A szerepkör beállításához tekintse meg az alábbi minta engedély [RestorableAction]() . | Nem alkalmazható | Nem alkalmazható  |
|Microsoft.DocumentDB/databaseAccounts/írás | Ez az engedély szükséges a fiók erőforráscsoporthoz való visszaállításához | Az az erőforráscsoport, amelyben a visszaállított fiókot létrehozták. | Az előfizetés, amelyben a visszaállított fiókot létrehozták |
|Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/Restore/Action |Ez az engedély szükséges a forrás helyreállítható adatbázis-fiók hatókörében, hogy lehetővé váljon a visszaállítási műveletek végrehajtása.  | A visszaállítani kívánt forrásoldali fiókhoz tartozó "RestorableDatabaseAccount" erőforrás. Ezt az értéket a helyreállítható adatbázis-fiók erőforrásának "ID" tulajdonsága is adja. Egy példa a visszaállítható fiókra `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>` | A helyreállítható adatbázis-fiókot tartalmazó előfizetés. Az erőforráscsoport nem választható hatókörként.  |
|Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/READ |Ez az engedély szükséges a forrás helyreállítható adatbázis-fiók hatókörében a visszaállítani kívánt adatbázis-fiókok listázásához.  | A visszaállítani kívánt forrásoldali fiókhoz tartozó "RestorableDatabaseAccount" erőforrás. Ezt az értéket a helyreállítható adatbázis-fiók erőforrásának "ID" tulajdonsága is adja. Egy példa a visszaállítható fiókra `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| A helyreállítható adatbázis-fiókot tartalmazó előfizetés. Az erőforráscsoport nem választható hatókörként.  |
|Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/*/READ | Ez az engedély szükséges a forrás helyreállítható fiók hatókörében, hogy lehetővé tegye a helyreállítható erőforrások, például az adatbázisok és tárolók beolvasását egy helyreállítható fiók számára.  | A visszaállítani kívánt forrásoldali fiókhoz tartozó "RestorableDatabaseAccount" erőforrás. Ezt az értéket a helyreállítható adatbázis-fiók erőforrásának "ID" tulajdonsága is adja. Egy példa a visszaállítható fiókra `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| A helyreállítható adatbázis-fiókot tartalmazó előfizetés. Az erőforráscsoport nem választható hatókörként. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Azure CLI szerepkör-hozzárendelési forgatókönyvek különböző hatókörökön történő visszaállításhoz

Az engedéllyel rendelkező szerepkörök különböző hatókörökhöz rendelhetők, így részletesen szabályozható, hogy ki hajthatja végre a visszaállítási műveletet egy előfizetésen vagy egy adott fiókon belül.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Az előfizetéshez tartozó bármely helyreállítható fiókból való visszaállítási képesség kiosztása

A beépített szerepkör kiosztása `CosmosRestoreOperator` előfizetési szinten

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Egy adott fiókból való visszaállítási képesség kiosztása

* Rendeljen hozzá egy felhasználói írási műveletet az adott erőforráscsoporthoz. Ez a művelet szükséges ahhoz, hogy új fiókot hozzon létre az erőforráscsoporthoz.

* Rendelje hozzá a "CosmosRestoreOperator" beépített szerepkört a helyreállítani kívánt helyreállítható adatbázis-fiókhoz. A következő parancsban a "RestorableDatabaseAccount" hatókörét a rendszer a kimenetében található "ID" tulajdonságból olvassa be `az cosmosdb restorable-database-account` (ha CLI-t használ), vagy  `Get-AzCosmosDBRestorableDatabaseAccount` (ha a PowerShellt használja).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Hozzárendelheti a képességet az erőforráscsoport bármely forrásoldali fiókjából történő visszaállításhoz.
Ez a művelet jelenleg nem támogatott.

## <a name="custom-role-creation-for-restore-action-with-cli"></a>Egyéni szerepkör létrehozása visszaállítási művelethez a CLI-vel

Az előfizetés tulajdonosa megadhatja az engedélyt bármely más Azure AD-identitásra való visszaállításhoz. A visszaállítási engedély a következő műveleten alapul: "Microsoft.DocumentDB/Locations/restorableDatabaseAccounts/Restore/Action", és szerepelnie kell a visszaállítási engedélyében. Létezik egy "CosmosRestoreOperator" nevű beépített szerepkör, amely tartalmazza ezt a szerepkört. Az engedélyt hozzárendelheti ehhez a beépített szerepkörhöz, vagy létrehozhat egy egyéni szerepkört is.

Az alábbi RestorableAction egy egyéni szerepkört jelöl. Explicit módon létre kell hoznia ezt a szerepkört. A következő JSON-sablon létrehoz egy "RestorableAction" nevű egyéni szerepkört visszaállítási engedéllyel:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Ezután a következő sablon-telepítési paranccsal hozzon létre egy visszaállítási engedéllyel rendelkező szerepkört az ARM-sablon használatával:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Következő lépések

* A [Azure Portal](continuous-backup-restore-portal.md), a [PowerShell](continuous-backup-restore-powershell.md), a [CLI](continuous-backup-restore-command-line.md)vagy a [Azure Resource Manager](continuous-backup-restore-template.md)használatával folyamatos biztonsági mentést konfigurálhat és kezelhet.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)
