---
title: Hálózati útválasztási beállítás konfigurálása
titleSuffix: Azure Storage
description: Konfigurálja az Azure Storage-fiók hálózati útválasztási beállításait annak megadásához, hogy a hálózati forgalom hogyan legyen átirányítva a fiókjához az interneten keresztül.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589854"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Hálózati útválasztási beállítások konfigurálása az Azure Storage-hoz

Ez a cikk azt ismerteti, hogyan konfigurálhatja a hálózati útválasztási beállításokat és az adott útvonalhoz tartozó végpontokat a Storage-fiókhoz. 

A hálózati útválasztási beállítások azt határozzák meg, hogy a hálózati forgalom hogyan legyen átirányítva a fiókjához az interneten keresztül. Az útvonal-specifikus végpontok olyan új végpontok, amelyeket az Azure Storage hoz létre a Storage-fiókhoz. Ezek a végpontok az alapértelmezett útválasztási beállítások módosítása nélkül irányítják át a forgalmat a kívánt útvonalon. További információ: [hálózati útválasztási preferencia az Azure Storage](network-routing-preference.md)-hoz.

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Az alapértelmezett nyilvános végpont útválasztási beállításainak konfigurálása

Alapértelmezés szerint a Storage-fiók nyilvános végpontjának útválasztási beállításai a Microsoft globális hálózat értékre vannak beállítva. A Microsoft globális hálózat és az internetes útválasztás lehetőség közül választhat alapértelmezett útválasztási előnyként a Storage-fiók nyilvános végpontja számára. Ha többet szeretne megtudni a két típusú útválasztás közötti különbségről, tekintse meg az Azure Storage-hoz készült [hálózati útválasztási beállítások](network-routing-preference.md)című témakört. 

### <a name="portal"></a>[Portál](#tab/azure-portal)

Az útválasztási preferencia módosítása az internetes útválasztásra:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigáljon a Storage-fiókjához a portálon.

3. A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Hálózatkezelés menüpont](./media/configure-network-routing-preference/networking-option.png)

4.  A **tűzfalak és virtuális hálózatok** lapon a **hálózati útválasztás** területen módosítsa az **útválasztási** beállítások beállítást az **Internet-útválasztás** értékre.

5.  Kattintson a **Mentés** gombra.

    > [!div class="mx-imgBorder"]
    > ![internetes útválasztási beállítás](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat a hitelesítéshez.

   ```powershell
   Connect-AzAccount
   ```

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

3. Ha módosítani szeretné az útválasztási beállításokat az internetes útválasztásra, használja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot, és állítsa a paramétert a következőre: `--routing-choice` `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Cserélje le a `<resource-group-name>` helyőrző értékét a Storage-fiókot tartalmazó erőforráscsoport nevére.

   Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Jelentkezzen be az Azure-előfizetésbe.

   - Azure Cloud Shell indításához jelentkezzen be a [Azure Portalba](https://portal.azure.com).

   - A CLI helyi telepítésére való bejelentkezéshez futtassa az az [login](/cli/azure/reference-index#az-login) parancsot:

     ```azurecli
     az login
     ```
2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

3. Ha módosítani szeretné az útválasztási beállításokat az internetes útválasztásra, használja az az [Storage Account Update](/cli/azure/storage/account#az_storage_account_update) parancsot, és állítsa a paramétert a következőre: `--routing-choice` `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

---

## <a name="configure-a-route-specific-endpoint"></a>Útvonal-specifikus végpont konfigurálása

Egy útvonal-specifikus végpont is konfigurálható. Megadhatja például az alapértelmezett végpont útválasztási beállításait az *internetes útválasztás* beállításnál, majd közzétehet egy útvonal-specifikus végpontot, amely lehetővé teszi az interneten lévő ügyfelek és a Storage-fiók közötti adatforgalmat a Microsoft globális hálózatán keresztül.

Ez a beállítás csak az útvonal-specifikus végpontra van hatással. Ez a beállítás nem befolyásolja az alapértelmezett útválasztási beállítást.  

### <a name="portal"></a>[Portál](#tab/azure-portal)

1.  Navigáljon a Storage-fiókjához a portálon.

2.  A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget.

3.  A **tűzfalak és virtuális hálózatok** lap **útvonal-specifikus végpontok közzététele** területén válassza az útvonal-specifikus végpont útválasztási beállításait, majd kattintson a **Mentés** gombra.

    A következő képen a kiválasztott **Microsoft hálózati útválasztási** lehetőség látható.

    > [!div class="mx-imgBorder"]
    > ![Microsoft hálózati útválasztási lehetőség](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Egy Route-specifikus végpont konfigurálásához használja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot. 

   - Ha olyan útvonal-specifikus végpontot szeretne létrehozni, amely a Microsoft hálózati útválasztási beállításait használja, állítsa a paramétert a következőre: `-PublishMicrosoftEndpoint` `true` . 

   - Az internetes útválasztási beállításokat használó útvonal-specifikus végpont létrehozásához állítsa a paramétert a következőre: `-PublishInternetEndpointTo` `true` .  

   A következő példa egy útvonal-specifikus végpontot hoz létre, amely a Microsoft hálózati útválasztási beállításait használja.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Cserélje le a `<resource-group-name>` helyőrző értékét a Storage-fiókot tartalmazó erőforráscsoport nevére.

   Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Egy Route-specifikus végpont konfigurálásához használja az az [Storage Account Update](/azure/storage/account#az-storage-account-update) parancsot. 

   - Ha olyan útvonal-specifikus végpontot szeretne létrehozni, amely a Microsoft hálózati útválasztási beállításait használja, állítsa a paramétert a következőre: `--publish-microsoft-endpoints` `true` . 

   - Az internetes útválasztási beállításokat használó útvonal-specifikus végpont létrehozásához állítsa a paramétert a következőre: `--publish-internet-endpoints` `true` .  

   A következő példa egy útvonal-specifikus végpontot hoz létre, amely a Microsoft hálózati útválasztási beállításait használja.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>A végpont nevének megkeresése egy adott útválasztási végponthoz

Ha egy útvonal-specifikus végpontot konfigurált, a végpontot a Storage-fiók tulajdonságainál találja.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1.  A **Beállítások** területen válassza a **Tulajdonságok** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Tulajdonságok menü lehetőség](./media/configure-network-routing-preference/properties.png)

2.  A **Microsoft hálózati útválasztási** végpontja minden olyan szolgáltatás esetében megjelenik, amely támogatja az útválasztási beállításokat. Ez a rendszerkép a blob és a Fájlszolgáltatások végpontját jeleníti meg.

    > [!div class="mx-imgBorder"]
    > ![Microsoft hálózati útválasztási beállítás az útválasztási specifikus végpontokhoz](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. A végpontok konzolra való kinyomtatásához használja a `PrimaryEndpoints` Storage-fiók objektum tulajdonságát.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Cserélje le a `<resource-group-name>` helyőrző értékét a Storage-fiókot tartalmazó erőforráscsoport nevére.

   Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. A végpontok konzolra való kinyomtatásához használja az az [Storage Account show](/cli/azure/storage/account#az_storage_account_show) tulajdonságot a Storage Account objektumhoz.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Cserélje le a `<resource-group-name>` helyőrző értékét a Storage-fiókot tartalmazó erőforráscsoport nevére.

   Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.

---

## <a name="see-also"></a>Lásd még

- [Hálózati útválasztási beállítások](network-routing-preference.md)
- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
