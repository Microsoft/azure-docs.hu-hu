---
title: Hálózati útválasztási beállítás konfigurálása
titleSuffix: Azure Storage
description: Konfigurálja az Azure-tárfiók hálózati útválasztási beállítását annak megadásához, hogy a rendszer hogyan irányíthatja a hálózati forgalmat a fiókba az interneten keresztül az ügyfelektől.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790454"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Hálózati útválasztási beállítás konfigurálása az Azure Storage-hoz

Ez a cikk bemutatja, hogyan konfigurálhatja a hálózati útválasztási beállításokat és az útvonal-specifikus végpontokat a tárfiókhoz. 

A hálózati útválasztási beállítás határozza meg, hogyan lesz irányítva a hálózati forgalom a fiókhoz az ügyfelektől az interneten keresztül. Az útvonal-specifikus végpontok új végpontok, amelyek az Azure Storage-ban létrejönnek a tárfiókhoz. Ezek a végpontok az alapértelmezett útválasztási beállítások módosítása nélkül irányítják a forgalmat a kívánt útvonalon. További információ: Hálózati [útválasztási beállítások az Azure Storage-hoz.](network-routing-preference.md)

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Az alapértelmezett nyilvános végpont útválasztási beállításának konfigurálása

Alapértelmezés szerint a tárfiók nyilvános végpontjának útválasztási beállítása a Microsoft globális hálózata. A tárfiók nyilvános végpontjának alapértelmezett útválasztási beállításaként választhat a Microsoft globális hálózata és az internetes útválasztás között. További információ a két útválasztási típus közötti különbségről: Hálózati útválasztási [beállítások az Azure Storage-hoz.](network-routing-preference.md) 

### <a name="portal"></a>[Portál](#tab/azure-portal)

Az útválasztási beállítás módosítása internetes útválasztásra:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a tárfiókját a portálon.

3. A **Beállítások alatt** válassza a Hálózat **lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Hálózat menüpont](./media/configure-network-routing-preference/networking-option.png)

4.  A **Tűzfalak és virtuális hálózatok** lap Hálózati útválasztás csoportjában **módosítsa** az Útválasztási **beállítások** beállítást internetes **útválasztásra.**

5.  Kattintson a **Mentés** gombra.

    > [!div class="mx-imgBorder"]
    > ![internetes útválasztási lehetőség](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Jelentkezzen be az Azure-előfizetésbe az paranccsal, és kövesse a képernyőn megjelenő `Connect-AzAccount` utasításokat a hitelesítéshez.

   ```powershell
   Connect-AzAccount
   ```

2. Ha az identitása több előfizetéshez is társítva van, állítsa az aktív előfizetést a statikus webhelyet tároló tárfiók előfizetésére.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetése azonosítójára.

3. Az útválasztási beállítás internetes útválasztásra való váltásához használja a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot, és állítsa a paramétert a `--routing-choice` következőre: `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Cserélje le a helyőrző értékét a tárfiókot tartalmazó erőforráscsoport `<resource-group-name>` nevére.

   Cserélje le `<storage-account-name>` a helyőrző értékét a tárfiók nevére.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Jelentkezzen be az Azure-előfizetésbe.

   - A Azure Cloud Shell elindításához jelentkezzen be a [Azure Portal.](https://portal.azure.com)

   - A parancssori felület helyi telepítésére való bejelentkezéshez futtassa [az az login](/cli/azure/reference-index#az_login) parancsot:

     ```azurecli
     az login
     ```
2. Ha az identitása több előfizetéshez is társítva van, állítsa be az aktív előfizetést a statikus webhelyet tároló tárfiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetése azonosítójára.

3. Az útválasztási beállítás internetes útválasztásra való váltásához használja [az az storage account update](/cli/azure/storage/account#az_storage_account_update) parancsot, és állítsa a `--routing-choice` paramétert a következőre: `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Cserélje le `<storage-account-name>` a helyőrző értékét a tárfiókja nevére.

---

## <a name="configure-a-route-specific-endpoint"></a>Útvonal-specifikus végpont konfigurálása

Útvonal-specifikus végpontot is konfigurálhat. Beállíthatja például az alapértelmezett végpont útválasztási beállítását internetes útválasztásra, majd közzétehet egy útvonal-specifikus végpontot, amely lehetővé teszi az internetes ügyfelek és a tárfiók közötti forgalom átirányítását a Microsoft globális hálózatán keresztül.

Ez a beállítás csak az útvonalspecifikus végpontra van hatással. Ez a beállítás nincs hatással az alapértelmezett útválasztási beállításra.  

### <a name="portal"></a>[Portál](#tab/azure-portal)

1.  Lépjen a tárfiókra a portálon.

2.  A **Beállítások alatt** válassza a Hálózat **lehetőséget.**

3.  A **Tűzfalak és virtuális** hálózatok lap Útvonalspecifikus végpontok közzététele csoportjában válassza ki az útvonalspecifikus végpont útválasztási beállítását, majd kattintson a Mentés **gombra.**

    Az alábbi képen a **Microsoft hálózati útválasztási lehetőség van kiválasztva.**

    > [!div class="mx-imgBorder"]
    > ![A Microsoft hálózati útválasztási lehetősége](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Útvonal-specifikus végpont konfigurálhoz használja a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot. 

   - A Microsoft hálózati útválasztási beállítását használó útvonal-specifikus végpont létrehozásához állítsa a paramétert a `-PublishMicrosoftEndpoint` következőre: `true` . 

   - Az internetes útválasztási beállítást használó útvonal-specifikus végpont létrehozásához állítsa a paramétert a `-PublishInternetEndpointTo` következőre: `true` .  

   Az alábbi példa egy útvonal-specifikus végpontot hoz létre, amely a Microsoft hálózati útválasztási beállítását használja.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Cserélje le a helyőrző értékét a tárfiókot tartalmazó `<resource-group-name>` erőforráscsoport nevére.

   Cserélje le `<storage-account-name>` a helyőrző értékét a tárfiók nevére.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Útvonal-specifikus végpont konfigurálhoz használja az [az storage account update](/azure/storage/account#az_storage_account_update) parancsot. 

   - A Microsoft hálózati útválasztási beállítását használó útvonal-specifikus végpont létrehozásához állítsa a paramétert a `--publish-microsoft-endpoints` következőre: `true` . 

   - Az internetes útválasztási beállítást használó útvonal-specifikus végpont létrehozásához állítsa a paramétert a `--publish-internet-endpoints` következőre: `true` .  

   Az alábbi példa egy útvonal-specifikus végpontot hoz létre, amely a Microsoft hálózati útválasztási beállítását használja.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Cserélje le `<storage-account-name>` a helyőrző értékét a tárfiók nevére.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Útvonalspecifikus végpont végpontnevének megkeresve

Ha útvonal-specifikus végpontot konfigurált, a végpontot a tárfiók tulajdonságai között találja.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1.  A **Beállítások alatt** válassza a Tulajdonságok **lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![tulajdonságok menüpont](./media/configure-network-routing-preference/properties.png)

2.  A **Microsoft hálózati útválasztási** végpontja minden olyan szolgáltatásnál megjelenik, amely támogatja az útválasztási beállításokat. Ezen a képen a blob- és fájlszolgáltatások végpontja látható.

    > [!div class="mx-imgBorder"]
    > ![A Microsoft hálózati útválasztási lehetősége útvonal-specifikus végpontok esetén](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. A végpontok konzolra való nyomtatásához használja `PrimaryEndpoints` a tárfiók objektum tulajdonságát.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Cserélje le a helyőrző értékét a tárfiókot tartalmazó `<resource-group-name>` erőforráscsoport nevére.

   Cserélje le `<storage-account-name>` a helyőrző értékét a tárfiók nevére.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A végpontok konzolra való nyomtatásához használja a tárfiók [objektum az storage account show](/cli/azure/storage/account#az_storage_account_show) tulajdonságát.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Cserélje le a helyőrző értékét a tárfiókot tartalmazó erőforráscsoport `<resource-group-name>` nevére.

   Cserélje le `<storage-account-name>` a helyőrző értékét a tárfiók nevére.

---

## <a name="see-also"></a>Lásd még

- [Hálózati útválasztási beállítás](network-routing-preference.md)
- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)
- [Biztonsági javaslatok a Blob Storage-hez](../blobs/security-recommendations.md)
