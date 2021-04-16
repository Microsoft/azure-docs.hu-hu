---
title: Azure Storage-fiók áthelyezése egy másik régióba | Microsoft Docs
description: Bemutatja, hogyan lehet áthelyezni egy Azure Storage-fiókot egy másik régióba.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 1900326bf03c6a32f25c7a019d8bd1e460735bd6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505598"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Azure Storage-fiók áthelyezése másik régióba

A tárfiókok áthelyezéséhez létre kell hozni egy másolatot a tárfiókról egy másik régióban. Ezután helyezze át az adatokat a fiókba az AzCopy vagy egy másik választott eszköz használatával.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
> 
> * Sablon exportálása.
> * Módosítsa a sablont a célterület és a tárfiók nevének hozzáadásával.
> * Telepítse a sablont az új tárfiók létrehozásához.
> * Konfigurálja az új tárfiókot.
> * Adatok áthelyezése az új tárfiókba.
> * Törölje a forrás régióban található erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

- Ellenőrizze, hogy a tárfiók által használt szolgáltatások és funkciók támogatottak-e a célként megadott régióban.

- Az előzetes verziójú funkciókhoz győződjön meg arról, hogy az előfizetése fel van listára va a célterületen.

<a id="prepare"></a>

## <a name="prepare"></a>Előkészítés

Első lépésekhez exportálja, majd módosítsa a Resource Manager sablont. 

### <a name="export-a-template"></a>Sablon exportálása

Ez a sablon a tárfiókot leíró beállításokat tartalmazza. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Sablon exportálása az Azure Portallal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza **a Minden erőforrás lehetőséget,** majd válassza ki a tárfiókját.

3. Válassza > **Automation**  >  **exportálási sablont.**

4. A **Sablon exportálása** **panelen** válassza a Letöltés lehetőséget.

5. Keresse meg a portálról letöltött .zip fájlt, és csomagolja ki a fájlt egy ön által választott mappába.

   Ez a zip-fájl tartalmazza a sablont és a sablon üzembe helyezéséhez szükséges szkripteket tartalmazó .json-fájlokat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sablon exportálása a PowerShell használatával:

1. Jelentkezzen be az Azure-előfizetésbe a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Ha az identitása több előfizetéshez is társítva van, állítsa be az aktív előfizetést az áthelyezni kívánt tárfiók előfizetéseként.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportálja a forrásként használt tárfiók sablonját. Ezek a parancsok json-sablont ment az aktuális könyvtárba.

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

A sablon üzembe helyezése a következő Azure Portal:

1. A Azure Portal válassza az **Erőforrás létrehozása lehetőséget.**

2. A **Keresés a Marketplace-en mezőbe írja** be a sablon üzembe **helyezését,** majd nyomja le az ENTER **billentyűt.**

3. Válassza a **Template deployment** lehetőséget.

    ![Azure Resource Manager-sablonkönyvtár](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Válassza a **Létrehozás** lehetőséget.

5. Válassza **a Build your own template (Saját sablon létrehozása a szerkesztőben) lehetőséget.**

6. Válassza **a Fájl** betöltése lehetőséget, majd  kövesse az utasításokat aztemplate.jsaz előző szakaszban letöltött fájlba való betöltéséhez.

7. Atemplate.js **fájlban** nevezze el a cél tárfiókot a tárfiók nevének alapértelmezett értékének beállításával. Ez a példa a tárfiók nevének alapértelmezett értékét állítja be `mytargetaccount` értékre.
    
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
    A régió helykódját az [Azure-helyek területen szerezheti be.](https://azure.microsoft.com/global-infrastructure/locations/)  A régió kódja a régió neve szóköz nélkül, **USA középső**  =  **régiója.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A sablon üzembe helyezése a PowerShell használatával:

1. Atemplate.js **fájlban** nevezze el a cél tárfiókot a tárfiók nevének alapértelmezett értékének beállításával. Ez a példa a tárfiók nevének alapértelmezett értékét állítja be `mytargetaccount` értékre.
    
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

2. **Szerkessze a fájlban** **template.jshely tulajdonságot** a célterületre. Ez a példa a célterületet a következőre állítja `eastus` be: .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    A régiókódokat a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancs futtatásával szerezheti be.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Áthelyezés

A sablon üzembe helyezésével új tárfiókot hozhat létre a célrégióban. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Mentse a **template.jsfájlt.**

2. Adja meg vagy válassza ki a tulajdonságértékeket:

- **Előfizetés:** Válasszon ki egy Azure-előfizetést.

- **Erőforráscsoport**: Válassza az **Új létrehozása** lehetőséget, majd adjon nevet az erőforráscsoportnak.

- **Hely:** Válasszon ki egy Azure-helyet.

3. Jelölje be az Elfogadom a fenti **feltételeket** és feltételeket jelölőnégyzetet, majd kattintson a **Vásárlás kiválasztása gombra.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Szerezze be azt az előfizetés-azonosítót, ahol üzembe szeretné helyezni a cél nyilvános [IP-címet a Get-AzSubscription használatával:](/powershell/module/az.accounts/get-azsubscription)

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. A sablon üzembe helyezéséhez használja az alábbi parancsokat:

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
| **Életciklus-felügyeleti szabályzatok** | [Az Azure Blob Storage-életciklus felügyelete](../blobs/storage-lifecycle-management-concepts.md) |
| **Statikus webhelyek** | [Statikus webhely tárolása az Azure Storage-ban](../blobs/storage-blob-static-website-how-to.md) |
| **Esemény-előfizetések** | [Reagálás Blob Storage-eseményekre](../blobs/storage-blob-event-overview.md) |
| **Riasztások** | [Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése a Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Egyéni tartományokkal Azure CDN blobok ELÉRÉSE HTTPS-kapcsolaton keresztül](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Ha beállít egy CDN-t a forrás tárfiókhoz, egyszerűen módosítsa a meglévő CDN forrását az új fiók elsődleges blob-szolgáltatásvégpontjára (vagy az elsődleges statikus webhely végpontjára). 

### <a name="move-data-to-the-new-storage-account"></a>Adatok áthelyezése az új tárfiókba

Az AzCopy az előnyben részesített eszköz az adatok áthelyezéséhez. Kiváló teljesítményre lett optimalizálva.  Ez egy gyorsabb módszer, mert az adatok másolása közvetlenül a Storage-kiszolgálók között történik, így az AzCopy nem használja a számítógép hálózati sávszélességét. Az AzCopyt a parancssorban vagy egy egyéni szkript részeként használhatja. Lásd: [Az AzCopy – első lépések.](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Az adatokat Azure Data Factory is használhatja. Intuitív felhasználói felületet biztosít. A Azure Data Factory tekintse meg az alábbi hivatkozások bármelyikét: 

  - [Adatok másolása az Azure Blob Storage-be vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-blob-storage)
  - [Adatok másolása az Azure Data Lake Storage Gen2-be vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-data-lake-storage)
  - [Adatok másolása az Azure File Storage-ba vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-file-storage)
  - [Adatok másolása az Azure Table Storage-be vagy onnan máshová az Azure Data Factoryvel](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása

Ha az üzembe helyezést követően újra szeretné kezdeni, törölheti a cél tárfiókot, [](#move) és megismételheti a jelen cikk Előkészítés [és](#prepare) áthelyezés szakaszának lépéseit.

A módosítások véglegesítéséhez és a tárfiók áthelyezésének befejezéséhez törölje a forrástárfiókot.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Tárfiók eltávolítása az Azure Portal használatával:

1. A Azure Portal bontsa ki a bal oldali menüt a szolgáltatások  menüjének megnyitásához, majd válassza a Tárfiókok lehetőséget a tárfiókok listájának megjelenítéséhez.

2. Keresse meg a törölni kívánt céltárfiókot, és kattintson a jobb gombbal a lista jobb oldalán található További gombra (**...**). 

3. Válassza **a Törlés lehetőséget,** majd erősítse meg.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és a társított erőforrások, köztük az új tárfiók eltávolításához használja a [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) parancsot:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban áthelyezett egy Azure-tárfiókot az egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Az erőforrások régiók közötti áthelyezésére és az Azure-beli vészhelyreállításra vonatkozó további információkért lásd:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../../site-recovery/azure-to-azure-tutorial-migrate.md)