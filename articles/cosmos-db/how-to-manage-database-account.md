---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megtudhatja, hogyan kezelheti Azure Cosmos DB erőforrásait a Azure Portal, a PowerShell, a CLI és a Azure Resource Manager sablonok használatával
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.openlocfilehash: d542e2b4e5db86fd3354514790e718f0694a09a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489752"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos-fiók kezelése
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk az Azure Cosmos-fiók különböző feladatainak az Azure Portal, az Azure PowerShell, az Azure CLI és az Azure Resource Manager-sablonok használatával történő kezelését ismerteti.

## <a name="create-an-account"></a>Fiók létrehozása

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Lásd: [Azure Cosmos db fiók létrehozása az Azure CLI-vel](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Tekintse meg [Azure Cosmos db fiók létrehozása a PowerShell használatával](manage-with-powershell.md#create-account) című témakört.

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager-sablon

Tekintse meg [Azure Cosmos db fiók létrehozása Azure Resource Manager-sablonokkal](./manage-with-templates.md) című témakört.

## <a name="addremove-regions-from-your-database-account"></a>Régiók hozzáadása és eltávolítása az adatbázisfiókból

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure Portal

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).

1. Nyissa meg az Azure Cosmos-fiókját, és nyissa meg az **adatreplikálás globális** menüjét.

1. Régiók hozzáadásához válassza ki a térképen a hatszögeket a **+** kívánt régió (k) nek megfelelő címkével. Másik lehetőségként a régió hozzáadásához válassza a **+ régió hozzáadása** lehetőséget, és válasszon egy régiót a legördülő menüből.

1. A régiók eltávolításához törölje a térképen egy vagy több régiót úgy, hogy a kék hatszögeket jelöli a pipa jelekkel. Vagy válassza a 🗑 jobb oldalon található régió melletti "szemétkosár" () ikont.

1. A módosítások mentéséhez kattintson **az OK gombra**.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Régiók hozzáadása vagy eltávolítása menü":::

Egyrégiós írási módban nem távolíthatja el az írási régiót. Az aktuális írási régió törlése előtt feladatátvételt kell végeznie egy másik régióba.

Többrégiós írási módban szabadon adhat hozzá és távolíthat el régiókat, ha marad legalább egy régió.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

További információ: [régiók hozzáadása vagy eltávolítása az Azure CLI-vel](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

További információ: [régiók hozzáadása vagy eltávolítása a PowerShell](manage-with-powershell.md#update-account) -lel

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Több írási régió konfigurálása

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure Portal

Nyissa meg az **adatreplikálás globálisan** lapot, és válassza az **Engedélyezés** lehetőséget a többrégiós írások engedélyezéséhez. Miután engedélyezte a többrégiós írást, az összes olyan olvasási régió, amelyre jelenleg a fiók tartozik, olvasási és írási régiók lesznek.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Az Azure Cosmos-fiók konfigurálja a többrégiós írások képernyőképét":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Lásd: [több írási régió engedélyezése az Azure CLI-vel](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Lásd: [több írási régió engedélyezése a PowerShell](manage-with-powershell.md#multi-region-writes) -lel

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Resource Manager-sablon

Egy fiók a fiók és a beállítás létrehozásához használt Resource Manager-sablon üzembe helyezésével egyetlen írási régióból több írási régióba is telepíthető `enableMultipleWriteLocations: true` . A következő Azure Resource Manager sablon egy egyszerű sablon, amely az SQL API-hoz két régióval és több írási hellyel rendelkező Azure Cosmos-fiókot helyez üzembe.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Automatikus feladatátvétel engedélyezése az Azure Cosmos-fiókhoz

Az automatikus feladatátvételi beállítás lehetővé teszi, hogy az Azure Cosmos DB a legmagasabb feladatátvételi prioritással rendelkező régióba feladatátvételt hajtson végre, ha egy adott régió elérhetetlenné válik. Ha engedélyezve van az automatikus feladatátvétel, a régió prioritását módosíthatja. A fióknak két vagy több régióval kell rendelkeznie az automatikus feladatátvétel engedélyezéséhez.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Az Azure Cosmos-fiókjából nyissa meg az **adatok replikálása globális** ablaktáblát.

2. A panel tetején válassza az **automatikus feladatátvétel** lehetőséget.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Adatok globális replikálása menü":::

3. Az **automatikus feladatátvétel** ablaktáblán **Győződjön meg arról**, hogy az **automatikus feladatátvétel engedélyezése** beállítás be értékre van állítva. 

4. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Automatikus feladatátvétel menü a portálon":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Lásd: [az automatikus feladatátvétel engedélyezése az Azure CLI-vel](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Lásd: [az automatikus feladatátvétel engedélyezése a PowerShell](manage-with-powershell.md#enable-automatic-failover) -lel

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Az Azure Cosmos-fiók feladatátvételi prioritásainak beállítása

Ha egy Cosmos-fiók automatikus feladatátvételre van konfigurálva, a régiók feladatátvételi prioritása módosítható.

> [!IMPORTANT]
> Az írási régió (nulla feladatátvételi prioritása) nem módosítható, ha a fiók konfigurálva van az automatikus feladatátvételre. Az írási régió módosításához le kell tiltania az automatikus feladatátvételt, és manuális feladatátvételt kell végrehajtania.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Az Azure Cosmos-fiókjából nyissa meg az **adatok replikálása globális** ablaktáblát.

2. A panel tetején válassza az **automatikus feladatátvétel** lehetőséget.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Adatok globális replikálása menü":::

3. Az **automatikus feladatátvétel** ablaktáblán **Győződjön meg arról**, hogy az **automatikus feladatátvétel engedélyezése** beállítás be értékre van állítva.

4. A feladatátvételi prioritás módosításához húzza az olvasási régiókat a sor bal oldalán lévő három pontra, amikor az egérmutatót a fölé viszi.

5. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Automatikus feladatátvétel menü a portálon":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Lásd: [a feladatátvételi prioritás beállítása az Azure CLI-vel](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Lásd: [a feladatátvételi prioritás beállítása a PowerShell](manage-with-powershell.md#modify-failover-priority) -lel

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Manuális feladatátvétel végrehajtása egy Azure Cosmos DB-fiókon

> [!IMPORTANT]
> Ahhoz, hogy a művelet sikeres legyen, az Azure Cosmos-fióknak konfigurálva kell lennie a manuális feladatátvételhez.

A manuális feladatátvétel végrehajtásának folyamata magában foglalja a fiók írási régiójának (feladatátvételi prioritás = 0) a fiókhoz konfigurált másik régióba való módosítását.

> [!NOTE]
> A több írási régióval rendelkező fiókok feladatátvétele nem végezhető el manuálisan. Az Azure Cosmos SDK-t használó alkalmazások esetében az SDK felismeri, ha egy régió elérhetetlenné válik, majd automatikusan átirányítja a következő legközelebbi régióra.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Nyissa meg az Azure Cosmos-fiókját, és nyissa meg az **adatreplikálás globális** menüjét.

2. A menü tetején válassza a **manuális feladatátvétel** lehetőséget.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Adatok globális replikálása menü":::

3. A **manuális feladatátvétel** menüben válassza ki az új írási régiót. Jelölje be a jelölőnégyzetet, és jelezze, hogy ezt a lehetőséget az írási régió módosításakor adja meg.

4. A feladatátvétel elindításához kattintson **az OK gombra**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Manuális feladatátvétel menü a portálon":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Lásd: [a manuális feladatátvétel elindítása az Azure CLI-vel](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Lásd: [a manuális feladatátvétel elindítása a PowerShell](manage-with-powershell.md#trigger-manual-failover) -lel

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos-fiók, valamint az adatbázis és a tárolók kezelésével kapcsolatos további információkért és példákért olvassa el a következő cikkeket:

* [Azure Cosmos DB kezelése Azure PowerShell használatával](manage-with-powershell.md)
* [Az Azure Cosmos DB kezelése az Azure CLI-vel](manage-with-cli.md)