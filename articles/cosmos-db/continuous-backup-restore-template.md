---
title: Az ARM-sablon használatával konfigurálhatja a folyamatos biztonsági mentést és az időpontra történő visszaállítást Azure Cosmos DBban.
description: Megtudhatja, hogyan helyezhet üzembe egy fiókot a folyamatos biztonsági mentéssel és az adathelyreállítással Azure Resource Manager sablonok használatával.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6c388a08a589cc89d83b7178e31e3f4497b924bb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527551"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-resource-manager-templates"></a>A folyamatos biztonsági mentés és az időpontra történő visszaállítás konfigurálása és kezelése Azure Resource Manager sablonok használatával
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB időponthoz kapcsolódó visszaállítási funkciója segít helyreállítani a tárolón belüli véletlen változásokból, a törölt fiókok, adatbázisok vagy tárolók visszaállítását, illetve bármely régióba való visszaállítását (ahol a biztonsági másolatok léteztek). A folyamatos biztonsági mentési mód lehetővé teszi, hogy az elmúlt 30 napban bármikor visszaállítsa az adott időpontra.

Ez a cikk bemutatja, hogyan helyezhet üzembe egy fiókot a folyamatos biztonsági mentéssel és az adathelyreállítással Azure Resource Manager sablonok használatával.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Folyamatos biztonsági mentéssel rendelkező fiók kiépítése

Azure Resource Manager-sablonok használatával folyamatos üzemmódú Azure Cosmos DB-fiókot helyezhet üzembe. A fiók kiépítéséhez szükséges sablon definiálásakor adja meg a "backupPolicy" paramétert az alábbi példában látható módon:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Ezután telepítse a sablont Azure PowerShell vagy parancssori felület használatával. Az alábbi példa bemutatja, hogyan helyezheti üzembe a sablont egy CLI-paranccsal:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Visszaállítás a Resource Manager-sablon használatával

A fiókot a Resource Manager-sablonnal is visszaállíthatja. A sablon definiálásakor a következő paramétereket kell megadnia:

* A "createMode" paraméter beállítása "visszaállítás" értékre
* Adja meg a "restoreParameters" értéket, figyelje meg, hogy a "restoreSource" érték ki van kinyerve a `az cosmosdb restorable-database-account list` forrás fiókjához tartozó parancs kimenetében. A rendszer a fiókhoz tartozó instance ID attribútumot használja a visszaállításhoz.
* Állítsa a "restoreMode" paramétert "PointInTime" értékre, és konfigurálja a "restoreTimestampInUtc" értéket.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Ezután telepítse a sablont Azure PowerShell vagy parancssori felület használatával. Az alábbi példa bemutatja, hogyan helyezheti üzembe a sablont egy CLI-paranccsal:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Következő lépések

* A folyamatos biztonsági mentés konfigurálása és kezelése az [Azure CLI](continuous-backup-restore-command-line.md), a [PowerShell](continuous-backup-restore-command-line.md)vagy a [Azure Portal](continuous-backup-restore-portal.md)használatával.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.