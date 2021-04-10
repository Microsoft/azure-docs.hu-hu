---
title: Az ARM-sablon használatával konfigurálhatja a folyamatos biztonsági mentést és az időpontra történő visszaállítást Azure Cosmos DBban.
description: Megtudhatja, hogyan helyezhet üzembe egy fiókot a folyamatos biztonsági mentéssel és az adathelyreállítással Azure Resource Manager sablonok használatával.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381817"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>A folyamatos biztonsági mentés és az időponthoz tartozó visszaállítás (előzetes verzió) konfigurálása és kezelése Azure Resource Manager sablonok használatával
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB az időponthoz tartozó visszaállítási funkció (előzetes verzió) segítségével helyreállíthatja a tárolók véletlen változását, a törölt fiókok, adatbázisok vagy tárolók visszaállítását, illetve bármely régióba való visszaállítást (ahol a biztonsági másolatok léteztek). A folyamatos biztonsági mentési mód lehetővé teszi, hogy az elmúlt 30 napban bármikor visszaállítsa az adott időpontra.

Ez a cikk bemutatja, hogyan helyezhet üzembe egy fiókot a folyamatos biztonsági mentéssel és az adathelyreállítással Azure Resource Manager sablonok használatával.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Folyamatos biztonsági mentéssel rendelkező fiók kiépítése

Azure Resource Manager-sablonok használatával folyamatos üzemmódú Azure Cosmos DB-fiókot helyezhet üzembe. A fiók kiépítéséhez szükséges sablon definiálásakor a `backupPolicy` következő példában látható módon adja meg a paramétert:

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

* Állítsa `createMode` vissza a paramétert a *visszaállításhoz*
* Adja meg a `restoreParameters` következőt:, figyelje meg, hogy a `restoreSource` rendszer kinyeri az értéket a `az cosmosdb restorable-database-account list` forrás fiókjához tartozó parancs kimenetében. A rendszer a fiókhoz tartozó instance ID attribútumot használja a visszaállításhoz.
* Állítsa a `restoreMode` paramétert a *PointInTime* és az `restoreTimestampInUtc` érték konfigurálására.

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