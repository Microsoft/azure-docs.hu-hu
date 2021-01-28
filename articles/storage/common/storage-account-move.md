---
title: Azure Storage-fiók áthelyezése másik régióba | Microsoft Docs
description: Bemutatja, hogyan helyezhető át egy Azure Storage-fiók egy másik régióba.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 76482266f0bbb1f80c1699c736048c705b90b4dc
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986920"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Azure Storage-fiók áthelyezése másik régióba

A tárfiókok áthelyezéséhez létre kell hozni egy másolatot a tárfiókról egy másik régióban. Ezután helyezze át az adatait a fiókba a AzCopy használatával vagy más választott eszközzel.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
> 
> * Sablon exportálása.
> * Módosítsa a sablont úgy, hogy hozzáadja a célként megadott régiót és a Storage-fiók nevét.
> * A sablon üzembe helyezésével hozza létre az új Storage-fiókot.
> * Konfigurálja az új Storage-fiókot.
> * Helyezze át az adatátvitelt az új Storage-fiókba.
> * Törölje az erőforrásokat a forrás régióban.

## <a name="prerequisites"></a>Előfeltételek

- Ellenőrizze, hogy a tárfiók által használt szolgáltatások és funkciók támogatottak-e a célként megadott régióban.

- Az előzetes verziójú funkciók esetében ellenőrizze, hogy az előfizetése allowlisted-e a célként megadott régióban.

<a id="prepare"></a>

## <a name="prepare"></a>Előkészítés

Első lépésként exportálja, majd módosítsa a Resource Manager-sablont. 

### <a name="export-a-template"></a>Sablon exportálása

Ez a sablon a tárfiókot leíró beállításokat tartalmazza. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Sablon exportálása az Azure Portallal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Storage-fiókját.

3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.

4. Válassza a **Letöltés** lehetőséget a **sablon exportálása** panelen.

5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába.

   Ez a zip-fájl tartalmazza azokat a. JSON fájlokat, amelyek tartalmazzák a sablont és a parancsfájlokat a sablon telepítéséhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sablon exportálása a PowerShell használatával:

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Ha az identitás egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést az áthelyezni kívánt Storage-fiók előfizetésére.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportálja a forrásként szolgáló Storage-fiók sablonját. Ezek a parancsok egy JSON-sablont mentenek az aktuális könyvtárba.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>A sablon módosítása 

A sablont a tárfióknév és a régió megváltoztatásával módosíthatja.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A sablon üzembe helyezése Azure Portal használatával:

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.

2. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **template Deployment** lehetőséget.

    ![Azure Resource Manager-sablonkönyvtár](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Válassza a **Létrehozás** lehetőséget.

5. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.

6. Válassza a **fájl betöltése** lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött fájl **template.js** betöltéséhez.

7. A fájl **template.js** a Storage-fiók nevének alapértelmezett értékének megadásával nevezze el a cél Storage-fiókot. Ez a példa a Storage-fiók nevének alapértelmezett értékét állítja be értékre `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA**  =  **CentralUS**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A sablon üzembe helyezése a PowerShell használatával:

1. A fájl **template.js** a Storage-fiók nevének alapértelmezett értékének megadásával nevezze el a cél Storage-fiókot. Ez a példa a Storage-fiók nevének alapértelmezett értékét állítja be értékre `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Szerkessze a **Location (hely** ) tulajdonságot a fájl **template.jsjában** a célként megadott régióban. Ebben a példában a célként megadott régiót állítja be `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    A [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancs futtatásával lekérheti a régiókódokat.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Áthelyezés

A sablon üzembe helyezésével új tárfiókot hozhat létre a célrégióban. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Mentse a **template.js** fájlt.

2. Adja meg vagy válassza ki a tulajdonságértékek értékét:

- **Előfizetés**: válasszon ki egy Azure-előfizetést.

- **Erőforráscsoport**: Válassza az **Új létrehozása** lehetőséget, majd adjon nevet az erőforráscsoportnak.

- **Hely**: válasszon ki egy Azure-helyet.

3. Jelölje **be az Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet, majd kattintson a **Vásárlás kiválasztása** gombra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Szerezze be azt az előfizetés-azonosítót, amelyben a cél nyilvános IP-címet a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)használatával szeretné telepíteni:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. A következő parancsok használatával telepítheti a sablont:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Az új tárfiók konfigurálása

Néhány funkció nem exportálható sablonként, ezért hozzá kell adnia őket az új tárfiókhoz. 

Az alábbi táblázat ezeket a funkciókat sorolja fel, és útmutatást nyújt az új tárfiókhoz való hozzáadásukhoz.

| Szolgáltatás    | Útmutató    |
|--------|-----------|
| **Életciklus-kezelési szabályzatok** | [Az Azure Blob Storage-életciklus felügyelete](../blobs/storage-lifecycle-management-concepts.md) |
| **Statikus webhelyek** | [Statikus webhely üzemeltetése az Azure Storage-ban](../blobs/storage-blob-static-website-how-to.md) |
| **Esemény-előfizetések** | [Reagálás Blob Storage-eseményekre](../blobs/storage-blob-event-overview.md) |
| **Riasztások** | [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Az egyéni tartományokkal rendelkező Blobok elérése a Azure CDN használatával HTTPS-kapcsolaton keresztül](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Ha a forrásként szolgáló Storage-fiókhoz állít be egy CDN-t, csak módosítsa a meglévő CDN eredetét az új fiók elsődleges blob Service-végpontján (vagy az elsődleges statikus webhely végpontján). 

### <a name="move-data-to-the-new-storage-account"></a>Adatok áthelyezése az új tárfiókba

Az AzCopy az előnyben részesített eszköz, amellyel áthelyezheti az adatait. Kiváló teljesítményre lett optimalizálva.  Ez egy gyorsabb módszer, mert az adatok másolása közvetlenül a Storage-kiszolgálók között történik, így az AzCopy nem használja a számítógép hálózati sávszélességét. Az AzCopyt a parancssorban vagy egy egyéni szkript részeként használhatja. Lásd: Ismerkedés [a AzCopy szolgáltatással](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

A Azure Data Factory használatával is áthelyezheti az adatait. Intuitív felhasználói felületet biztosít. A Azure Data Factory használatához tekintse meg a következő hivatkozások bármelyikét:. 

  - [Adatok másolása az Azure Blob Storage-be vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-blob-storage)
  - [Adatok másolása az Azure Data Lake Storage Gen2-be vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-data-lake-storage)
  - [Adatok másolása az Azure File Storage-ba vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-file-storage)
  - [Adatok másolása az Azure Table Storage-be vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása

Ha az üzembe helyezést követően el szeretné indítani a műveletet, törölheti a cél Storage-fiókot, és megismételheti a cikk [előkészítés](#prepare) és [Áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások véglegesítéséhez és a tárfiók áthelyezésének befejezéséhez törölje a forrástárfiókot.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Tárfiók eltávolítása az Azure Portal használatával:

1. A Azure Portalban bontsa ki a bal oldalon található menüt a szolgáltatások menüjének megnyitásához, majd válassza a **Storage-fiókok** lehetőséget a Storage-fiókok listájának megjelenítéséhez.

2. Keresse meg a törölni kívánt cél Storage-fiókot, és kattintson a jobb gombbal a lista jobb oldalán található **további** gombra (**...**).

3. Válassza a **Törlés** lehetőséget, és erősítse meg.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és a hozzá tartozó erőforrások eltávolításához, beleértve az új Storage-fiókot is, használja a [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) parancsot:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Storage-fiókot helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../../site-recovery/azure-to-azure-tutorial-migrate.md)